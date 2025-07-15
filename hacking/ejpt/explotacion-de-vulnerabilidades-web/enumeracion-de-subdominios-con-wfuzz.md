# Enumeración de Subdominios con Wfuzz

Veremos cómo realizar una búsqueda de subdominios con más herramientas de fuzzing; y para ello lo diferenciaremos bien con la búsqueda de directorios web, ya que se tratan de conceptos distintos.

Enlace para descargar la máquina vulnerable:

[https://hackmyvm.eu/machines/machine.php?vm=Logan](https://hackmyvm.eu/machines/machine.php?vm=Logan)

* Hacer reconocimiento de la red para encontrar la máquina objetiva:

```bash
└─# arp-scan -I eth0 --localnet
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	(Unknown)
192.168.1.15	f4:7b:09:52:3d:48	(Unknown)
192.168.1.34	08:00:27:9d:d6:db	(Unknown)
192.168.1.14	de:66:3f:1f:fb:5b	(Unknown: locally administered)
192.168.1.12	1c:98:c1:ab:90:69	(Unknown)
192.168.1.13	fe:d5:2d:56:5a:08	(Unknown: locally administered)
192.168.1.11	d8:1e:dd:32:a2:9d	(Unknown)

7 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.043 seconds (125.31 hosts/sec). 7 responded
```

* Comprobar conectividad con la máquina objetiva:

```bash
└─# ping -c 1 192.168.1.34
PING 192.168.1.34 (192.168.1.34) 56(84) bytes of data.
64 bytes from 192.168.1.34: icmp_seq=1 ttl=64 time=3.48 ms

--- 192.168.1.34 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 3.481/3.481/3.481/0.000 ms
```

* Escanear puertos de la máquina objetivo:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.34
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-21 20:45 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
Initiating ARP Ping Scan at 20:45
Scanning 192.168.1.34 [1 port]
Completed ARP Ping Scan at 20:45, 0.21s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 20:45
Scanning 192.168.1.34 [65535 ports]
Discovered open port 80/tcp on 192.168.1.34
Discovered open port 25/tcp on 192.168.1.34
Completed SYN Stealth Scan at 20:45, 9.18s elapsed (65535 total ports)
Initiating Service scan at 20:45
Scanning 2 services on 192.168.1.34
Completed Service scan at 20:45, 6.10s elapsed (2 services on 1 host)
NSE: Script scanning 192.168.1.34.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.41s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 1.32s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
Nmap scan report for 192.168.1.34
Host is up, received arp-response (0.0011s latency).
Scanned at 2024-10-21 20:45:01 CEST for 18s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
25/tcp open  smtp    syn-ack ttl 64 Postfix smtpd
|_smtp-commands: logan.hmv, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
| ssl-cert: Subject: commonName=logan
| Subject Alternative Name: DNS:logan
| Issuer: commonName=logan
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-07-03T13:46:49
| Not valid after:  2033-06-30T13:46:49
| MD5:   9b0d:3da4:7274:a99c:8b9e:705a:122f:249f
| SHA-1: ef60:c55c:e4bf:e99a:c4bb:3281:f2c4:ded5:d44b:4801
| -----BEGIN CERTIFICATE-----
| MIIC7DCCAdSgAwIBAgIUAlr/UnIZGJp5n3bGtfiPosfmoh4wDQYJKoZIhvcNAQEL
| BQAwEDEOMAwGA1UEAwwFbG9nYW4wHhcNMjMwNzAzMTM0NjQ5WhcNMzMwNjMwMTM0
| NjQ5WjAQMQ4wDAYDVQQDDAVsb2dhbjCCASIwDQYJKoZIhvcNAQEBBQADggEPADCC
| AQoCggEBALA1lkF4fnRQLkvBGoCsRJlInBrwk8yxe8jWue068b2Q7Ti3rNtGhf6G
| Ze7FX1Yjb5NH1KTTcqumNb/nAxv9hMl4Dc50MC2hSWq6qMnqOrkb+AI16bVuhYcm
| SLGsuq7bPGflyhfoIK8Cj0xXvZS65D68pBhoZ/7Oji5rGMfngnrcJ2Q36Ctimm8b
| UK+exEWSgbC12xd2f/noFWBPrB7EC5XHMYARVV6/I17aajheOqEKBTDL0AkSqzKT
| /snWlz7vjMGjJsNIZ6o6wZUYxqXzFDXnInyQ7k+IPXcDDp7V3TOZAB6jQwZhdH2o
| 1+CGuwBbCWvV+kAGvlTotL7k9RN5F/cCAwEAAaM+MDwwCQYDVR0TBAIwADAQBgNV
| HREECTAHggVsb2dhbjAdBgNVHQ4EFgQUeVAVq//+vvbEd+bXwnPsAxY6HxAwDQYJ
| KoZIhvcNAQELBQADggEBADScL3LqV9/XFprgMf6GOz8y2lvbkOSADTvFHUQiBcqp
| /K/LWCXRtHJVkJA5z5+IMAFWGfueBffgdZLnKtyCLfUtsMqqqoVR0BXlzPys1Jhm
| Ri4Ra9KVvH7pxt69kD+3xk7Hz8jyHQVfXWGmPZ/li6OOQxKei69CwDqTvcvjyNyc
| lcix3P+eTIDcnWHFu2wkOew8+q7Mza7IzfIy6u3qs5Lqccv1fMDhDYMQ2j5iGEo9
| SZMyBUTRqR2nmX8wuL6wYHcmvfWp0Px3bAXxaqz2p96W3XTqPECTJ45rcfRsdbFB
| SIh86N9X05gerFhkaKhczHfR1hjtyUb1LltfRhbh7cY=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.52 (Ubuntu)
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
MAC Address: 08:00:27:9D:D6:DB (Oracle VirtualBox virtual NIC)
Service Info: Host:  logan.hmv

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.01s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.48 seconds
           Raw packets sent: 65536 (2.884MB) | Rcvd: 65536 (2.621MB)
```

* Hacer virtualhosting con el dominio logan.hmv:

```bash
└─# cat /etc/hosts
127.0.0.1	localhost
127.0.1.1	XXY.home	XXY

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

192.168.1.34 logan.hmv
```

* Buscar subdominios para el dominio logan.hmv:

```bash
└─# wfuzz -c --hc=404,400 --hl=1 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -H "Host: FUZZ.logan.hmv" -u "http://logan.hmv"
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://logan.hmv/
Total requests: 207643

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                      
=====================================================================

000000256:   200        62 L     101 W      1112 Ch     "admin"                                                                                      

Total time: 0
Processed Requests: 207643
Filtered Requests: 207642
Requests/sec.: 0
```

* Añadimos el subdominio en el archivo /etc/hosts para poder hacer virtualhosting:

```bash
└─# cat /etc/hosts
127.0.0.1	localhost
127.0.1.1	XXY.home	XXY

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

192.168.1.34 logan.hmv admin.logan.hmv
```
