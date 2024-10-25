# Reconocimiento de la red con ARP SCAN o NETDISCOVER

Herramientas para hacer reconocimiento de la red para encontrar equipos conectados.

## ARP-SCAN

* Hacer reconocimiento con arp-scan para encontrar equipos en la red:

```bash
└─$ sudo arp-scan -I eth0 --localnet
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	Sagemcom Broadband SAS
192.168.1.15	f4:7b:09:52:3d:48	Intel Corporate
192.168.1.24	08:00:27:b3:f1:e0	PCS Systemtechnik GmbH
192.168.1.23	d2:7d:e9:5c:60:ea	(Unknown: locally administered)
192.168.1.25	08:00:27:f6:7a:39	PCS Systemtechnik GmbH
192.168.1.28	74:40:be:9e:eb:4a	LG Innotek

6 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.509 seconds (102.03 hosts/sec). 6 responded
```

## Netdiscover

* Hacer reconocimiento con netdiscover para encontrar equipos en la red:

```bash
└─$ sudo netdiscover -i eth0 -r 192.168.1.0/24

 Currently scanning: Finished!   |   Screen View: Unique Hosts                                                                                               
                                                                                                                                                             
 13 Captured ARP Req/Rep packets, from 6 hosts.   Total size: 780                                                                                            
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.1.1     a8:9a:93:66:15:50      7     420  Sagemcom Broadband SAS                             
 192.168.1.15    f4:7b:09:52:3d:48      1      60  Intel Corporate                                    
 192.168.1.24    08:00:27:b3:f1:e0      1      60  PCS Systemtechnik GmbH                             
 192.168.1.25    08:00:27:f6:7a:39      1      60  PCS Systemtechnik GmbH                             
 192.168.1.28    74:40:be:9e:eb:4a      1      60  LG Innotek                                         
 192.168.1.11    d8:1e:dd:32:a2:9d      2     120  GUANGDONG OPPO MOBILE TELECOMMUNICATIONS CORP.,LTD
```

## Nmap

* Hacer reconocimiento con nmap para encontrar equipos en la red:

```bash
└─$ sudo nmap -sn 192.168.1.0/24
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-06 02:55 CEST
Nmap scan report for liveboxfibra.home (192.168.1.1)
Host is up (0.0064s latency).
MAC Address: A8:9A:93:66:15:50 (Sagemcom Broadband SAS)
Nmap scan report for oppo-ax7.home (192.168.1.11)
Host is up (0.054s latency).
MAC Address: D8:1E:DD:32:A2:9D (Guangdong Oppo Mobile Telecommunications)
Nmap scan report for red.home (192.168.1.15)
Host is up (0.00042s latency).
MAC Address: F4:7B:09:52:3D:48 (Intel Corporate)
Nmap scan report for uclient.home (192.168.1.24)
Host is up (0.00073s latency).
MAC Address: 08:00:27:B3:F1:E0 (Oracle VirtualBox virtual NIC)
Nmap scan report for mario-pc.home (192.168.1.25)
Host is up (0.00080s latency).
MAC Address: 08:00:27:F6:7A:39 (Oracle VirtualBox virtual NIC)
Nmap scan report for lgwebostv.home (192.168.1.28)
Host is up (0.0048s latency).
MAC Address: 74:40:BE:9E:EB:4A (LG Innotek)
Nmap scan report for xxy-1.home (192.168.1.18)
Host is up.
Nmap done: 256 IP addresses (7 hosts up) scanned in 2.11 seconds
```
