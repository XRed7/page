# Cómo Hacer un Ataque de Transferencia de Zona DNS

Un concepto realmente importante dentro del hacking y las redes, es el uso de los servidores DNS; y es por ello que vamos a aprender cómo identificarlo dentro de un escaneo de nmap, así como entender el funcionamiento de los ataques de transferencia de zona, los cuales nos van a servir para encontrar más subdominios, para añadirlos al archivo hosts y así acceder a ellos.

Enlace para descargar la máquina vulnerable:

[https://vulnyx.com/#hunter](https://vulnyx.com/#hunter)

* Hacer reconocimiento de la red para encontrar la máquina objetivo:

```bash
└─# arp-scan -I eth0 --localnet                                                                                            
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	(Unknown)
192.168.1.15	f4:7b:09:52:3d:48	(Unknown)
192.168.1.28	74:40:be:9e:eb:4a	(Unknown)
192.168.1.113	08:00:27:da:47:55	(Unknown)
192.168.1.11	d8:1e:dd:32:a2:9d	(Unknown)

5 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 1.996 seconds (128.26 hosts/sec). 5 responded
```

* Comprobar conectividad con la máquina objetivo:

```bash
└─# ping -c 1 192.168.1.113
PING 192.168.1.113 (192.168.1.113) 56(84) bytes of data.
64 bytes from 192.168.1.113: icmp_seq=1 ttl=64 time=2.26 ms

--- 192.168.1.113 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 2.264/2.264/2.264/0.000 ms
```

* Escanear puertos de la máquina objetivo:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.113
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-21 21:32 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:32
Completed NSE at 21:32, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:32
Completed NSE at 21:32, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:32
Completed NSE at 21:32, 0.00s elapsed
Initiating ARP Ping Scan at 21:32
Scanning 192.168.1.113 [1 port]
Completed ARP Ping Scan at 21:32, 0.11s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 21:32
Scanning 192.168.1.113 [65535 ports]
Discovered open port 22/tcp on 192.168.1.113
Discovered open port 80/tcp on 192.168.1.113
Discovered open port 53/tcp on 192.168.1.113
SYN Stealth Scan Timing: About 50.87% done; ETC: 21:33 (0:00:31 remaining)
Completed SYN Stealth Scan at 21:33, 68.57s elapsed (65535 total ports)
Initiating Service scan at 21:33
Scanning 3 services on 192.168.1.113
Completed Service scan at 21:34, 16.05s elapsed (3 services on 1 host)
NSE: Script scanning 192.168.1.113.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:34
Completed NSE at 21:34, 8.20s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:34
Completed NSE at 21:34, 0.02s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:34
Completed NSE at 21:34, 0.00s elapsed
Nmap scan report for 192.168.1.113
Host is up, received arp-response (0.00056s latency).
Scanned at 2024-10-21 21:32:41 CEST for 93s
Not shown: 51914 filtered tcp ports (no-response), 13618 closed tcp ports (reset)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 64 OpenSSH 7.9p1 Debian 10+deb10u3 (protocol 2.0)
| ssh-hostkey: 
|   2048 f7:ea:48:1a:a3:46:0b:bd:ac:47:73:e8:78:25:af:42 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKps92PT0Mo49rFdHP7epTdmPPCd/fSHO75dUfDZkuQqPs3NW1DqCVF5LFERWictYZhSMxfvWEQnFKHEMdlUuAJ0kbgQGqOn6jH3tiQ8jyhrmRmZSDLG0s2TOm91MiqV84nxMnMTnOs5ufpn9huVKj2solGKRvcp6KhHP+jgPrrG7Qzg2MBfwWdwgjUZXa9OsgQj/+XzjlBs0YHSKnkS+6cnck8Yk5h32Vus2yVRD8SSZafLpTb/OYxTWd4g1WH0eyOyTW19yVH6gBy5aSb7fqk2eObti+zW7a/+PxISlgmfieGILfZcQRUFKwkP7McHXIbxB0jANL4+h7pZ2fLU2p
|   256 2e:41:ca:86:1c:73:ca:de:ed:b8:74:af:d2:06:5c:68 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBE/I2mv1nRyQZ1F6NzQGerQMGqYQedUN6S2snwsUMS3W+RyqulPLPn599ZWeckjP021MqP9qsZjq6lUICSI5xMg=
|   256 33:6e:a2:58:1c:5e:37:e1:98:8c:44:b1:1c:36:6d:75 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAvNdJQVZtNWIJXKf5oQ5ysPy6wq9WNetvWn9g1yOqdL
53/tcp open  domain  syn-ack ttl 64 (unknown banner: not currently available)
| dns-nsid: 
|_  bind.version: not currently available
| fingerprint-strings: 
|   DNSVersionBindReqTCP: 
|     version
|     bind
|_    currently available
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.38 ((Debian))
| http-robots.txt: 1 disallowed entry 
|_hunterzone.nyx
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.38 (Debian)
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-TCP:V=7.94SVN%I=7%D=10/21%Time=6716ACA9%P=x86_64-pc-linux-gnu%r(
SF:DNSVersionBindReqTCP,52,"\0P\0\x06\x85\0\0\x01\0\x01\0\x01\0\0\x07versi
SF:on\x04bind\0\0\x10\0\x03\xc0\x0c\0\x10\0\x03\0\0\0\0\0\x18\x17not\x20cu
SF:rrently\x20available\xc0\x0c\0\x02\0\x03\0\0\0\0\0\x02\xc0\x0c");
MAC Address: 08:00:27:DA:47:55 (Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:34
Completed NSE at 21:34, 0.01s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:34
Completed NSE at 21:34, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:34
Completed NSE at 21:34, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 93.66 seconds
           Raw packets sent: 123692 (5.442MB) | Rcvd: 13624 (544.968KB)
```

* Añadimos el dominio hunterzone.nyx encontrado para hacer virtualhosting:

```bash
└─# cat /etc/hosts
127.0.0.1	localhost
127.0.1.1	XXY.home	XXY

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

192.168.1.113 hunterzone.nyx
```

* Hacer ataque de transferencia de zonas DNS:

```bash
└─# dig axfr hunterzone.nyx @192.168.1.113

; <<>> DiG 9.20.2-1-Debian <<>> axfr hunterzone.nyx @192.168.1.113
;; global options: +cmd
hunterzone.nyx.		604800	IN	SOA	ns1.hunterzone.nyx. root.hunterzone.nyx. 2 604800 86400 2419200 604800
hunterzone.nyx.		604800	IN	NS	ns1.hunterzone.nyx.
?.hunterzone.nyx.	604800	IN	TXT	"devhunter.nyx"
admin.hunterzone.nyx.	604800	IN	A	127.0.0.1
cloud.hunterzone.nyx.	604800	IN	A	127.0.0.1
ftp.hunterzone.nyx.	604800	IN	A	127.0.0.1
ns1.hunterzone.nyx.	604800	IN	A	127.0.0.1
www.hunterzone.nyx.	604800	IN	A	127.0.0.1
hunterzone.nyx.		604800	IN	SOA	ns1.hunterzone.nyx. root.hunterzone.nyx. 2 604800 86400 2419200 604800
;; Query time: 8 msec
;; SERVER: 192.168.1.113#53(192.168.1.113) (TCP)
;; WHEN: Mon Oct 21 21:38:24 CEST 2024
;; XFR size: 9 records (messages 1, bytes 294)
```

* Añadimos el dominio devhunter.nyx encontrado para hacer virtualhosting:

```bash
└─# cat /etc/hosts
127.0.0.1	localhost
127.0.1.1	XXY.home	XXY

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

192.168.1.113 hunterzone.nyx devhunter.nyx
```

* Buscar subdominios del dominio encontrado con wfuzz:

```bash
└─# wfuzz -c --hc=404,400 --hl=367 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -H "Host: FUZZ.devhunter.nyx" -u 192.168.1.113
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.1.113/
Total requests: 19966

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                      
=====================================================================

000000096:   200        26 L     51 W       525 Ch      "files"                                                                                      

Total time: 149.6737
Processed Requests: 19966
Filtered Requests: 19965
Requests/sec.: 133.3967
```
