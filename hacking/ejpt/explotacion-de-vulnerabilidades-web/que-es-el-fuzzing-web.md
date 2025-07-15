# Qué es el Fuzzing Web

## Uso de Gobuster

Una vez que hayamos accedido a una página web, es imprescindible realizar una búsqueda de directorios web para encontrar rutas donde podamos seguir accediendo y así encontrar más vulnerabilidades. Para ello tenemos herramientas como gobuster, que se ejecutan directamente desde terminal y simplemente necesita recibir la url que queramos analizar y también un diccionario.

Enlace para descargar la máquina metasploitable2:

[https://sourceforge.net/projects/metasploitable/files/Metasploitable2](https://sourceforge.net/projects/metasploitable/files/Metasploitable2)

* Hacer reconocimiento de la red para encontrar la máquina objetiva y comprobar conectividad con ella:

```bash
└─# arp-scan -I eth0 --localnet
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	Sagemcom Broadband SAS
192.168.1.15	f4:7b:09:52:3d:48	Intel Corporate
192.168.1.27	08:00:27:f7:5d:1f	PCS Systemtechnik GmbH
192.168.1.11	d8:1e:dd:32:a2:9d	GUANGDONG OPPO MOBILE TELECOMMUNICATIONS CORP.,LTD
192.168.1.28	74:40:be:9e:eb:4a	LG Innotek

5 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.276 seconds (112.48 hosts/sec). 5 responded

└─# ping -c 1 192.168.1.27                                    
PING 192.168.1.27 (192.168.1.27) 56(84) bytes of data.
64 bytes from 192.168.1.27: icmp_seq=1 ttl=64 time=2.41 ms

--- 192.168.1.27 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 2.409/2.409/2.409/0.000 ms
```

* Escanear puertos abiertos en la máquina objetiva con nmap:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.27
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-16 18:29 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:29
Completed NSE at 18:29, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:29
Completed NSE at 18:29, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:29
Completed NSE at 18:29, 0.00s elapsed
Initiating ARP Ping Scan at 18:29
Scanning 192.168.1.27 [1 port]
Completed ARP Ping Scan at 18:29, 0.12s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 18:29
Scanning 192.168.1.27 [65535 ports]
Discovered open port 53/tcp on 192.168.1.27
Discovered open port 80/tcp on 192.168.1.27
Discovered open port 21/tcp on 192.168.1.27
Discovered open port 25/tcp on 192.168.1.27
Discovered open port 445/tcp on 192.168.1.27
Discovered open port 23/tcp on 192.168.1.27
Discovered open port 5900/tcp on 192.168.1.27
Discovered open port 139/tcp on 192.168.1.27
Discovered open port 3306/tcp on 192.168.1.27
Discovered open port 111/tcp on 192.168.1.27
Discovered open port 22/tcp on 192.168.1.27
Discovered open port 35570/tcp on 192.168.1.27
Discovered open port 5432/tcp on 192.168.1.27
Discovered open port 8009/tcp on 192.168.1.27
Discovered open port 8787/tcp on 192.168.1.27
Discovered open port 2121/tcp on 192.168.1.27
Discovered open port 514/tcp on 192.168.1.27
Discovered open port 37430/tcp on 192.168.1.27
Discovered open port 59982/tcp on 192.168.1.27
Discovered open port 2049/tcp on 192.168.1.27
Discovered open port 6000/tcp on 192.168.1.27
Discovered open port 3632/tcp on 192.168.1.27
Discovered open port 6667/tcp on 192.168.1.27
Discovered open port 6697/tcp on 192.168.1.27
Discovered open port 1099/tcp on 192.168.1.27
Discovered open port 59999/tcp on 192.168.1.27
Discovered open port 513/tcp on 192.168.1.27
Discovered open port 1524/tcp on 192.168.1.27
Discovered open port 512/tcp on 192.168.1.27
Discovered open port 8180/tcp on 192.168.1.27
Completed SYN Stealth Scan at 18:30, 11.01s elapsed (65535 total ports)
Initiating Service scan at 18:30
Scanning 30 services on 192.168.1.27
Completed Service scan at 18:32, 126.30s elapsed (30 services on 1 host)
NSE: Script scanning 192.168.1.27.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:32
NSE: [ftp-bounce 192.168.1.27:21] PORT response: 500 Illegal PORT command.
Completed NSE at 18:32, 8.71s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:32
Completed NSE at 18:32, 0.38s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:32
Completed NSE at 18:32, 0.01s elapsed
Nmap scan report for 192.168.1.27
Host is up, received arp-response (0.0012s latency).
Scanned at 2024-10-16 18:29:58 CEST for 147s
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE     REASON         VERSION
21/tcp    open  ftp         syn-ack ttl 64 vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.1.18
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp    open  ssh         syn-ack ttl 64 OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBALz4hsc8a2Srq4nlW960qV8xwBG0JC+jI7fWxm5METIJH4tKr/xUTwsTYEYnaZLzcOiy21D3ZvOwYb6AA3765zdgCd2Tgand7F0YD5UtXG7b7fbz99chReivL0SIWEG/E96Ai+pqYMP2WD5KaOJwSIXSUajnU5oWmY5x85sBw+XDAAAAFQDFkMpmdFQTF+oRqaoSNVU7Z+hjSwAAAIBCQxNKzi1TyP+QJIFa3M0oLqCVWI0We/ARtXrzpBOJ/dt0hTJXCeYisKqcdwdtyIn8OUCOyrIjqNuA2QW217oQ6wXpbFh+5AQm8Hl3b6C6o8lX3Ptw+Y4dp0lzfWHwZ/jzHwtuaDQaok7u1f971lEazeJLqfiWrAzoklqSWyDQJAAAAIA1lAD3xWYkeIeHv/R3P9i+XaoI7imFkMuYXCDTq843YU6Td+0mWpllCqAWUV/CQamGgQLtYy5S0ueoks01MoKdOMMhKVwqdr08nvCBdNKjIEd3gH6oBk/YRnjzxlEAYBsvCmM4a0jmhz0oNiRWlc/F+bkUeFKrBx/D2fdfZmhrGg==
|   2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAstqnuFMBOZvO3WTEjP4TUdjgWkIVNdTq6kboEDjteOfc65TlI7sRvQBwqAhQjeeyyIk8T55gMDkOD0akSlSXvLDcmcdYfxeIF0ZSuT+nkRhij7XSSA/Oc5QSk3sJ/SInfb78e3anbRHpmkJcVgETJ5WhKObUNf1AKZW++4Xlc63M4KI5cjvMMIPEVOyR3AKmI78Fo3HJjYucg87JjLeC66I7+dlEYX6zT8i1XYwa/L1vZ3qSJISGVu8kRPikMv/cNSvki4j+qDYyZ2E5497W87+Ed46/8P42LNGoOV8OcX/ro6pAcbEPUdUEfkJrqi2YXbhvwIJ0gFMb6wfe5cnQew==
23/tcp    open  telnet      syn-ack ttl 64 Linux telnetd
25/tcp    open  smtp        syn-ack ttl 64 Postfix smtpd
|_ssl-date: 2024-10-16T16:30:50+00:00; -1m35s from scanner time.
|_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|_    SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/emailAddress=root@ubuntu804-base.localdomain/organizationalUnitName=Office for Complication of Otherwise Simple Affairs/localityName=Everywhere
| Issuer: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/emailAddress=root@ubuntu804-base.localdomain/organizationalUnitName=Office for Complication of Otherwise Simple Affairs/localityName=Everywhere
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2010-03-17T14:07:45
| Not valid after:  2010-04-16T14:07:45
| MD5:   dcd9:ad90:6c8f:2f73:74af:383b:2540:8828
| SHA-1: ed09:3088:7066:03bf:d5dc:2373:99b4:98da:2d4d:31c6
| -----BEGIN CERTIFICATE-----
| MIIDWzCCAsQCCQD6+TpMf7a5zDANBgkqhkiG9w0BAQUFADCB8TELMAkGA1UEBhMC
| WFgxKjAoBgNVBAgTIVRoZXJlIGlzIG5vIHN1Y2ggdGhpbmcgb3V0c2lkZSBVUzET
| MBEGA1UEBxMKRXZlcnl3aGVyZTEOMAwGA1UEChMFT0NPU0ExPDA6BgNVBAsTM09m
| ZmljZSBmb3IgQ29tcGxpY2F0aW9uIG9mIE90aGVyd2lzZSBTaW1wbGUgQWZmYWly
| czEjMCEGA1UEAxMadWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4xLjAsBgkqhkiG
| 9w0BCQEWH3Jvb3RAdWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4wHhcNMTAwMzE3
| MTQwNzQ1WhcNMTAwNDE2MTQwNzQ1WjCB8TELMAkGA1UEBhMCWFgxKjAoBgNVBAgT
| IVRoZXJlIGlzIG5vIHN1Y2ggdGhpbmcgb3V0c2lkZSBVUzETMBEGA1UEBxMKRXZl
| cnl3aGVyZTEOMAwGA1UEChMFT0NPU0ExPDA6BgNVBAsTM09mZmljZSBmb3IgQ29t
| cGxpY2F0aW9uIG9mIE90aGVyd2lzZSBTaW1wbGUgQWZmYWlyczEjMCEGA1UEAxMa
| dWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4xLjAsBgkqhkiG9w0BCQEWH3Jvb3RA
| dWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4wgZ8wDQYJKoZIhvcNAQEBBQADgY0A
| MIGJAoGBANa0EzYzmpVxexvefIN12nGxPKl//q1kG3fpT66+ytT4y++uu0N5JHP/
| POWeO238yLGs+kxNXptMmVQL16hKULqp3h0f9ORrAqP0a0XNTK+NiWIzj2W7NmGf
| xCxzwU4uoKgUTphwRmG70bkx34yZ7nVreTxAoK6XAJCd3JkNM6S1AgMBAAEwDQYJ
| KoZIhvcNAQEFBQADgYEAkqS0uBRVYyVRSgvDKiLPOvgXagzPZqqnZS9Ibc3jPlyf
| d2zURFQfHoRPjtSN3awtiAkhqNpWLKkFPEloNRl1DNpTI4iIGS10JsEiZe4RaINq
| U0qcJ8ugtOmNKQyyPBhcZ8xTph4w0Komex6uQLkpAWwuvKIZlHwVbo0wOPbKLnU=
|_-----END CERTIFICATE-----
53/tcp    open  domain      syn-ack ttl 64 ISC BIND 9.4.2
| dns-nsid: 
|_  bind.version: 9.4.2
80/tcp    open  http        syn-ack ttl 64 Apache httpd 2.2.8 ((Ubuntu) DAV/2)
|_http-title: Metasploitable2 - Linux
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
111/tcp   open  rpcbind     syn-ack ttl 64 2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/udp   nfs
|   100005  1,2,3      54917/udp   mountd
|   100005  1,2,3      59982/tcp   mountd
|   100021  1,3,4      37430/tcp   nlockmgr
|   100021  1,3,4      46452/udp   nlockmgr
|   100024  1          35570/tcp   status
|_  100024  1          38981/udp   status
139/tcp   open  netbios-ssn syn-ack ttl 64 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn syn-ack ttl 64 Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
512/tcp   open  exec?       syn-ack ttl 64
513/tcp   open  login       syn-ack ttl 64 OpenBSD or Solaris rlogind
514/tcp   open  tcpwrapped  syn-ack ttl 64
1099/tcp  open  java-rmi    syn-ack ttl 64 GNU Classpath grmiregistry
1524/tcp  open  bindshell   syn-ack ttl 64 Metasploitable root shell
2049/tcp  open  nfs         syn-ack ttl 64 2-4 (RPC #100003)
2121/tcp  open  ftp         syn-ack ttl 64 ProFTPD 1.3.1
3306/tcp  open  mysql       syn-ack ttl 64 MySQL 5.0.51a-3ubuntu5
| mysql-info: 
|   Protocol: 10
|   Version: 5.0.51a-3ubuntu5
|   Thread ID: 8
|   Capabilities flags: 43564
|   Some Capabilities: LongColumnFlag, Support41Auth, SwitchToSSLAfterHandshake, SupportsTransactions, Speaks41ProtocolNew, SupportsCompression, ConnectWithDatabase
|   Status: Autocommit
|_  Salt: :m6cp+!cROu!@_>Uq[Rj
3632/tcp  open  distccd     syn-ack ttl 64 distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  syn-ack ttl 64 PostgreSQL DB 8.3.0 - 8.3.7
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/emailAddress=root@ubuntu804-base.localdomain/organizationalUnitName=Office for Complication of Otherwise Simple Affairs/localityName=Everywhere
| Issuer: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/emailAddress=root@ubuntu804-base.localdomain/organizationalUnitName=Office for Complication of Otherwise Simple Affairs/localityName=Everywhere
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2010-03-17T14:07:45
| Not valid after:  2010-04-16T14:07:45
| MD5:   dcd9:ad90:6c8f:2f73:74af:383b:2540:8828
| SHA-1: ed09:3088:7066:03bf:d5dc:2373:99b4:98da:2d4d:31c6
| -----BEGIN CERTIFICATE-----
| MIIDWzCCAsQCCQD6+TpMf7a5zDANBgkqhkiG9w0BAQUFADCB8TELMAkGA1UEBhMC
| WFgxKjAoBgNVBAgTIVRoZXJlIGlzIG5vIHN1Y2ggdGhpbmcgb3V0c2lkZSBVUzET
| MBEGA1UEBxMKRXZlcnl3aGVyZTEOMAwGA1UEChMFT0NPU0ExPDA6BgNVBAsTM09m
| ZmljZSBmb3IgQ29tcGxpY2F0aW9uIG9mIE90aGVyd2lzZSBTaW1wbGUgQWZmYWly
| czEjMCEGA1UEAxMadWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4xLjAsBgkqhkiG
| 9w0BCQEWH3Jvb3RAdWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4wHhcNMTAwMzE3
| MTQwNzQ1WhcNMTAwNDE2MTQwNzQ1WjCB8TELMAkGA1UEBhMCWFgxKjAoBgNVBAgT
| IVRoZXJlIGlzIG5vIHN1Y2ggdGhpbmcgb3V0c2lkZSBVUzETMBEGA1UEBxMKRXZl
| cnl3aGVyZTEOMAwGA1UEChMFT0NPU0ExPDA6BgNVBAsTM09mZmljZSBmb3IgQ29t
| cGxpY2F0aW9uIG9mIE90aGVyd2lzZSBTaW1wbGUgQWZmYWlyczEjMCEGA1UEAxMa
| dWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4xLjAsBgkqhkiG9w0BCQEWH3Jvb3RA
| dWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4wgZ8wDQYJKoZIhvcNAQEBBQADgY0A
| MIGJAoGBANa0EzYzmpVxexvefIN12nGxPKl//q1kG3fpT66+ytT4y++uu0N5JHP/
| POWeO238yLGs+kxNXptMmVQL16hKULqp3h0f9ORrAqP0a0XNTK+NiWIzj2W7NmGf
| xCxzwU4uoKgUTphwRmG70bkx34yZ7nVreTxAoK6XAJCd3JkNM6S1AgMBAAEwDQYJ
| KoZIhvcNAQEFBQADgYEAkqS0uBRVYyVRSgvDKiLPOvgXagzPZqqnZS9Ibc3jPlyf
| d2zURFQfHoRPjtSN3awtiAkhqNpWLKkFPEloNRl1DNpTI4iIGS10JsEiZe4RaINq
| U0qcJ8ugtOmNKQyyPBhcZ8xTph4w0Komex6uQLkpAWwuvKIZlHwVbo0wOPbKLnU=
|_-----END CERTIFICATE-----
|_ssl-date: 2024-10-16T16:30:50+00:00; -1m35s from scanner time.
5900/tcp  open  vnc         syn-ack ttl 64 VNC (protocol 3.3)
| vnc-info: 
|   Protocol version: 3.3
|   Security types: 
|_    VNC Authentication (2)
6000/tcp  open  X11         syn-ack ttl 64 (access denied)
6667/tcp  open  irc         syn-ack ttl 64 UnrealIRCd
6697/tcp  open  irc         syn-ack ttl 64 UnrealIRCd
| irc-info: 
|   users: 2
|   servers: 1
|   lusers: 2
|   lservers: 0
|   server: irc.Metasploitable.LAN
|   version: Unreal3.2.8.1. irc.Metasploitable.LAN 
|   uptime: 0 days, 0:15:49
|   source ident: nmap
|   source host: Test-366F7ED6.home
|_  error: Closing Link: edgijjeqd[xxy-1.home] (Quit: edgijjeqd)
8009/tcp  open  ajp13       syn-ack ttl 64 Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8180/tcp  open  http        syn-ack ttl 64 Apache Tomcat/Coyote JSP engine 1.1
|_http-server-header: Apache-Coyote/1.1
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/5.5
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
8787/tcp  open  drb         syn-ack ttl 64 Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
35570/tcp open  status      syn-ack ttl 64 1 (RPC #100024)
37430/tcp open  nlockmgr    syn-ack ttl 64 1-4 (RPC #100021)
59982/tcp open  mountd      syn-ack ttl 64 1-3 (RPC #100005)
59999/tcp open  java-rmi    syn-ack ttl 64 GNU Classpath grmiregistry
MAC Address: 08:00:27:F7:5D:1F (Oracle VirtualBox virtual NIC)
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 33259/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 18489/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 51549/udp): CLEAN (Failed to receive data)
|   Check 4 (port 30238/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 58m25s, deviation: 2h00m00s, median: -1m35s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
|_smb2-security-mode: Couldn't establish a SMBv2 connection.
| nbstat: NetBIOS name: METASPLOITABLE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   METASPLOITABLE<00>   Flags: <unique><active>
|   METASPLOITABLE<03>   Flags: <unique><active>
|   METASPLOITABLE<20>   Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1e>        Flags: <group><active>
| Statistics:
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_  00:00:00:00:00:00:00:00:00:00:00:00:00:00
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name: 
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
|_  System time: 2024-10-16T12:30:42-04:00

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:32
Completed NSE at 18:32, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:32
Completed NSE at 18:32, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:32
Completed NSE at 18:32, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 147.37 seconds
           Raw packets sent: 65536 (2.884MB) | Rcvd: 65536 (2.622MB)
```

* Uso de gobuster para encontrar directorios en el servidor web de la máquina metasploitable:

```bash
└─# gobuster dir -u http://192.168.1.27/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.1.27/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index                (Status: 200) [Size: 891]
/test                 (Status: 301) [Size: 316] [--> http://192.168.1.27/test/]
/twiki                (Status: 301) [Size: 317] [--> http://192.168.1.27/twiki/]
/tikiwiki             (Status: 301) [Size: 320] [--> http://192.168.1.27/tikiwiki/]
/phpinfo              (Status: 200) [Size: 47978]
/server-status        (Status: 403) [Size: 298]
Progress: 207643 / 207644 (100.00%)
===============================================================
Finished
===============================================================
```

* Uso de gobuster para encontrar directorios y archivos en el servidor web de la máquina metasploitable:

```bash
└─# gobuster dir -u http://192.168.1.27/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x txt,py,php,sh
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.1.27/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              txt,py,php,sh
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 891]
/index                (Status: 200) [Size: 891]
/test                 (Status: 301) [Size: 316] [--> http://192.168.1.27/test/]
/twiki                (Status: 301) [Size: 317] [--> http://192.168.1.27/twiki/]
/tikiwiki             (Status: 301) [Size: 320] [--> http://192.168.1.27/tikiwiki/]
/phpinfo              (Status: 200) [Size: 47978]
/phpinfo.php          (Status: 200) [Size: 47990]
/server-status        (Status: 403) [Size: 298]
```

## Otras Herramientas Fuzzing Web – Uso de WFUZZ, DIRB y DIRSEARCH

Veremos varias alternativas para realizar fuzzing web, ya que el entorno del examen EJPT es un laboratorio sin conexión a internet, y es por ello que resulta de gran utilidad conocer las alternativas que tenemos en caso de que una herramienta que necesitemos no se encuentre instalada.

Enlace para descargar la máquina vulnerable:

[https://vulnyx.com/#jenk](https://vulnyx.com/#jenk)

* Hacer reconocimiento de la red para encontrar la máquina objetiva y comprobar conectividad con ella:

```bash
└─# arp-scan -I eth0 --localnet
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	(Unknown)
192.168.1.15	f4:7b:09:52:3d:48	(Unknown)
192.168.1.20	72:3e:ee:27:01:89	(Unknown: locally administered)
192.168.1.28	74:40:be:9e:eb:4a	(Unknown)
192.168.1.78	08:00:27:74:06:4e	(Unknown)
192.168.1.11	d8:1e:dd:32:a2:9d	(Unknown)

6 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.224 seconds (115.11 hosts/sec). 6 responded
                                                                                                                                                              
┌──(root㉿XXY)-[/home/red]
└─# ping -c 1 192.168.1.78 
PING 192.168.1.78 (192.168.1.78) 56(84) bytes of data.
64 bytes from 192.168.1.78: icmp_seq=1 ttl=64 time=2.86 ms

--- 192.168.1.78 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 2.864/2.864/2.864/0.000 ms
```

* Escanear puertos abiertos en la máquina objetiva con nmap:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.78 
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-21 22:00 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:00
Completed NSE at 22:00, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:00
Completed NSE at 22:00, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:00
Completed NSE at 22:00, 0.00s elapsed
Initiating ARP Ping Scan at 22:00
Scanning 192.168.1.78 [1 port]
Completed ARP Ping Scan at 22:00, 0.19s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 22:00
Scanning 192.168.1.78 [65535 ports]
Discovered open port 8080/tcp on 192.168.1.78
Discovered open port 80/tcp on 192.168.1.78
Discovered open port 22/tcp on 192.168.1.78
Completed SYN Stealth Scan at 22:01, 46.20s elapsed (65535 total ports)
Initiating Service scan at 22:01
Scanning 3 services on 192.168.1.78
Completed Service scan at 22:01, 6.62s elapsed (3 services on 1 host)
NSE: Script scanning 192.168.1.78.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:01
Completed NSE at 22:01, 0.74s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:01
Completed NSE at 22:01, 0.08s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:01
Completed NSE at 22:01, 0.01s elapsed
Nmap scan report for 192.168.1.78
Host is up, received arp-response (0.00080s latency).
Scanned at 2024-10-21 22:00:33 CEST for 54s
Not shown: 35090 filtered tcp ports (no-response), 30442 closed tcp ports (reset)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 64 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 f0:e6:24:fb:9e:b0:7a:1a:bd:f7:b1:85:23:7f:b1:6f (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDP4OvUJ0xKoulS7xOYz1485bm/ZBVN/86xLQvh7Gqa1DmEWz/eHP2C3MJQnqTFPOEh18FULOzj9fiehyzhd6CM7+qBZ/4B9b5RkOx7AL+S3aRIey4qQj7/k72PqMBkyfD2krjNOg7ZZe8z9o0A4VyeDljG6ukVFeN6PEtWWtdmmnVJztgzX0wPWPaO9GM5hITyvpIB/Y/IqueYR+ft2n5ROLLUfjFLezB+zSa6xkDPGiY9qMZBMXA/6oaaD3TV1x6jfTtZi+Aca0scDfOTJUVlSwZYaHrJQSNlKFJhniucqq/zxOnMIHjs/v1YXYCh0jlYDsb5J/NqTzEPMKkbtwn97T5/FQvsWDGJFTtxvCCrInmnUHB+cG8dSRYQZ763QoPxF/feDSNbrKjTv8D1K2EPhf1rBGQGIObgatVHNFclVWfuq7sn4x9olNnbsEogIQ5mbEq0mBlgOW5vowFxUkI60Ond4Dl7H4fkCeiPfngWFrT+6cQoNgA3HRKf6NtQeYs=
|   256 99:c8:74:31:45:10:58:b0:ce:cc:63:b4:7a:82:57:3d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNDNbes4gKOy7nXoXxW1kPwOX/vuxNkae5WSrIFu+ZD8OUIX5OK8e6o7IZDJAxn/ACAJL9Mm+tA44syyemA6C40=
|   256 60:da:3e:31:38:fa:b5:49:ab:48:c3:43:2c:9f:d1:32 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINItrDSHbBfPB1CJosqklAQXN4/Mt++ocUqbiG861ZSG
80/tcp   open  http    syn-ack ttl 64 Apache httpd 2.4.56 ((Debian))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.56 (Debian)
|_http-title: Apache2 Debian Default Page: It works
8080/tcp open  http    syn-ack ttl 64 Jetty 10.0.13
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Jetty(10.0.13)
|_http-favicon: Unknown favicon MD5: 23E8C7BD78E8CD826C5A6073B15068B1
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
MAC Address: 08:00:27:74:06:4E (Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:01
Completed NSE at 22:01, 0.01s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:01
Completed NSE at 22:01, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:01
Completed NSE at 22:01, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.91 seconds
           Raw packets sent: 106868 (4.702MB) | Rcvd: 30448 (1.218MB)                                                 
```

Vemos que hay un servidor web corriendo en el puerto 80 y 8080.

### Uso de wfuzz

* Uso de wfuzz para encontrar directorios en el servidor web:

```bash
└─# wfuzz -c --hc=404,400 --hl=368 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt "http://192.168.1.78/FUZZ"
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.1.78/FUZZ
Total requests: 207643

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                      
=====================================================================

000003190:   301        9 L      28 W       314 Ch      "webcams"                                                                        

Total time: 0
Processed Requests: 207643
Filtered Requests: 207641
Requests/sec.: 0
```

### Uso de dirb

* Uso de dirb para encontrar directorios en el servidor web:

```bash
└─# dirb http://192.168.1.78

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Tue Oct 22 00:17:07 2024
URL_BASE: http://192.168.1.78/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.1.78/ ----
+ http://192.168.1.78/index.html (CODE:200|SIZE:10701)                                                                                                       
+ http://192.168.1.78/server-status (CODE:403|SIZE:277)                                                                                                      
                                                                                                                                                             
-----------------
END_TIME: Tue Oct 22 00:17:37 2024
DOWNLOADED: 4612 - FOUND: 2
```

### Uso de dirsearch

* Uso de dirsearch para encontrar directorios en el servidor web:

```bash
└─# dirsearch -u http://192.168.1.78 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 207628

Output File: /home/red/reports/http_192.168.1.78/_24-10-22_00-21-09.txt

Target: http://192.168.1.78/

[00:21:09] Starting: 
[00:22:43] 301 -  314B  - /webcams  ->  http://192.168.1.78/webcams/
[00:36:31] 403 -  277B  - /server-status

Task Completed
```
