# Escaneos Básicos con Nmap

Escaneos básicos con la herramienta nmap.

* Escaneo sin parámetros con nmap:

```bash
└─$ sudo nmap 192.168.1.25      
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-06 23:41 CEST
Nmap scan report for mario-pc.home (192.168.1.25)
Host is up (0.00074s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
5000/tcp open  upnp
MAC Address: 08:00:27:F6:7A:39 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 4.92 seconds
```

* Escaneo con parámetros con nmap:

```bash
└─$ sudo nmap -p- --open -sS -sC -sV --min-rate 2000 -n -vvv -Pn 192.168.1.25 -oN reporte
ççHost discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-06 23:53 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:53
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 0 undergoing Script Pre-Scan
NSE: Active NSE Script Threads: 1 (0 waiting)
NSE Timing: About 0.00% done
Completed NSE at 23:53, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:53
Completed NSE at 23:53, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:53
Completed NSE at 23:53, 0.00s elapsed
Initiating ARP Ping Scan at 23:53
Scanning 192.168.1.25 [1 port]
Completed ARP Ping Scan at 23:53, 0.07s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 23:53
Scanning 192.168.1.25 [65535 ports]
Discovered open port 139/tcp on 192.168.1.25
Discovered open port 135/tcp on 192.168.1.25
Discovered open port 445/tcp on 192.168.1.25
SYN Stealth Scan Timing: About 46.35% done; ETC: 23:54 (0:00:36 remaining)
Discovered open port 5000/tcp on 192.168.1.25
Completed SYN Stealth Scan at 23:54, 65.76s elapsed (65535 total ports)
Initiating Service scan at 23:54
Scanning 4 services on 192.168.1.25
Stats: 0:02:58 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 75.00% done; ETC: 23:56 (0:00:37 remaining)
Completed Service scan at 23:57, 161.93s elapsed (4 services on 1 host)
NSE: Script scanning 192.168.1.25.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:57
NSE Timing: About 99.82% done; ETC: 23:57 (0:00:00 remaining)
Completed NSE at 23:57, 40.09s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:57
Completed NSE at 23:57, 1.03s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:57
Completed NSE at 23:57, 0.00s elapsed
Nmap scan report for 192.168.1.25
Host is up, received arp-response (0.00100s latency).
Scanned at 2024-10-06 23:53:15 CEST for 269s
Not shown: 65531 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE      REASON          VERSION
135/tcp  open  msrpc        syn-ack ttl 128 Microsoft Windows RPC
139/tcp  open  netbios-ssn  syn-ack ttl 128 Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds syn-ack ttl 128 Windows 7 Ultimate 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
5000/tcp open  upnp?        syn-ack ttl 128
MAC Address: 08:00:27:F6:7A:39 (Oracle VirtualBox virtual NIC)
Service Info: Host: MARIO-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 11h20m01s, deviation: 4h02m29s, median: 9h00m00s
| smb-os-discovery: 
|   OS: Windows 7 Ultimate 7601 Service Pack 1 (Windows 7 Ultimate 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1
|   Computer name: mario-PC
|   NetBIOS computer name: MARIO-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-10-06T23:57:05-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| nbstat: NetBIOS name: MARIO-PC, NetBIOS user: <unknown>, NetBIOS MAC: 08:00:27:f6:7a:39 (Oracle VirtualBox virtual NIC)
| Names:
|   MARIO-PC<20>         Flags: <unique><active>
|   MARIO-PC<00>         Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1e>        Flags: <group><active>
| Statistics:
|   08:00:27:f6:7a:39:00:00:00:00:00:00:00:00:00:00:00
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_  00:00:00:00:00:00:00:00:00:00:00:00:00:00
| smb2-time: 
|   date: 2024-10-07T06:57:04
|_  start_date: 2024-10-07T06:22:03
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 60349/tcp): CLEAN (Timeout)
|   Check 2 (port 21093/tcp): CLEAN (Timeout)
|   Check 3 (port 11540/udp): CLEAN (Timeout)
|   Check 4 (port 41178/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:57
Completed NSE at 23:57, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:57
Completed NSE at 23:57, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:57
Completed NSE at 23:57, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 269.43 seconds
           Raw packets sent: 131118 (5.769MB) | Rcvd: 57 (2.488KB)
```

Recomendable en el examen del EJPT tener un min-rate de **2000** en el parámetro.

Este comando de nmap realiza un escaneo detallado de un host específico en la red, en este caso, `192.168.1.25`. Aquí te explico cada una de las opciones utilizadas:

1. `-p-`: Escanea todos los puertos (1-65535).
2. `--open`: Muestra solo los puertos abiertos.
3. `-sS`: Realiza un escaneo de puertos TCP SYN ("half-open"), que es más rápido y menos detectable por el sistema de defensa.
4. `-sC`: Utiliza los scripts predeterminados de Nmap, los cuales suelen incluir una variedad de comprobaciones comunes para obtener más información sobre el host.
5. `-sV`: Detecta la versión de los servicios que están ejecutándose en los puertos abiertos.
6. `--min-rate 2000`: Establece una tasa mínima de paquetes por segundo de 2000 para acelerar el escaneo.
7. `-n`: No realiza resolución DNS, lo que hace el escaneo más rápido si no se necesita resolver nombres.
8. `-vvv`: Nivel de verbosidad muy alto, que proporciona mucha información en pantalla sobre el progreso del escaneo.
9. `-Pn`: Omite el "ping" inicial (asumiendo que el host está en línea), lo que es útil cuando el host no responde a los pings pero tiene puertos abiertos.
10. `192.168.1.25`: Dirección IP del host objetivo.
11. `-oN reporte`: Guarda el resultado en un archivo llamado `reporte` en un formato de salida legible.

En resumen, este comando escanea todos los puertos de la dirección IP especificada, muestra solo los que están abiertos, detecta los servicios y versiones de los mismos, y guarda un informe detallado del resultado en un archivo llamado `reporte`.
