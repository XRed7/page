# Detectar Vulnerabilidades Con Nmap

Detectar vulnerabilidades con la herramienta nmap.

* Detectar si hay vulnerabilidades en el servicio 445 samba:

```bash
└─$ sudo nmap --script "vuln" -p445 192.168.1.25 
[sudo] password for red: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-07 00:11 CEST
Pre-scan script results:
| broadcast-avahi-dos: 
|   Discovered hosts:
|     224.0.0.251
|   After NULL UDP avahi packet DoS (CVE-2011-1002).
|_  Hosts are all up (not vulnerable).
Nmap scan report for mario-pc.home (192.168.1.25)
Host is up (0.0022s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 08:00:27:F6:7A:39 (Oracle VirtualBox virtual NIC)

Host script results:
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
|_smb-vuln-ms10-054: false
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
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED

Nmap done: 1 IP address (1 host up) scanned in 40.53 seconds
```

El comando `nmap --script "vuln" -p445 192.168.1.25` ejecuta un escaneo de vulnerabilidades específicas en el puerto 445 del host `192.168.1.25`. A continuación te explico los parámetros:

1. **`--script "vuln"`**: Utiliza el conjunto de scripts de Nmap categorizados como "vuln". Estos scripts buscan vulnerabilidades conocidas en el host objetivo, como exploits que afectan a ciertos servicios y configuraciones inseguras.
2. **`-p445`**: Escanea específicamente el puerto `445`. Este puerto es utilizado por el protocolo SMB (Server Message Block), el cual es conocido por tener una serie de vulnerabilidades, especialmente en versiones más antiguas de Windows.
3. **`192.168.1.25`**: Es la dirección IP del host que será escaneado.

El resultado del escaneo con el script `vuln` para el puerto 445 de la máquina `192.168.1.25` indica lo siguiente:

#### Información General:

* **Host**: `192.168.1.25` (Nombre del equipo: `mario-pc.home`).
* **Puerto**: `445/tcp` está abierto, utilizado por el servicio `microsoft-ds`.
* **Dirección MAC**: `08:00:27:F6:7A:39` (Oracle VirtualBox, sugiriendo que es una máquina virtual).

#### Resultados de Scripts de Vulnerabilidades:

1. **`smb-vuln-ms10-061`**:
   * Resultado: `NT_STATUS_ACCESS_DENIED`.
   * Esto indica que el script intentó comprobar si la máquina era vulnerable a la vulnerabilidad MS10-061, pero no tuvo permiso para hacerlo.
2. **`smb-vuln-ms10-054`**:
   * Resultado: `false`.
   * La máquina no es vulnerable a la vulnerabilidad MS10-054, que está relacionada con una falla en SMB que podría permitir ataques de denegación de servicio.
3. **`smb-vuln-ms17-010`**:
   * **Vulnerabilidad detectada**: Sí, la máquina es vulnerable a **MS17-010**, conocida como **EternalBlue**.
   * **Gravedad**: Alta (riesgo crítico).
   * **Descripción**: Esta es una vulnerabilidad de ejecución remota de código en servidores SMBv1. Fue explotada por el malware `WannaCry` y otros ataques masivos en 2017.
   * **ID de CVE**: `CVE-2017-0143`.
   * **Fecha de divulgación**: 14 de marzo de 2017.
   * **Referencias**:
     * [Guía de seguridad de Microsoft](https://technet.microsoft.com/en-us/library/security/ms17-010.aspx)
     * [Guía para ataques WannaCrypt](https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks)
     * [Información de CVE](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143)
4. **`samba-vuln-cve-2012-1182`**:
   * Resultado: `NT_STATUS_ACCESS_DENIED`.
   * El script no pudo determinar si la máquina era vulnerable debido a la falta de permisos.

#### Conclusiones y Recomendaciones:

* **Vulnerabilidad Crítica Detectada**: El host es vulnerable a **EternalBlue (MS17-010)**. Esta es una falla grave que permite la ejecución remota de código, lo cual podría ser utilizado para comprometer completamente la máquina.
* **Recomendación**:
  1. **Actualizar el Sistema Operativo**: Asegúrate de aplicar todos los parches de seguridad de Microsoft, específicamente el que mitiga MS17-010.
  2. **Deshabilitar SMBv1**: SMBv1 es una versión antigua y poco segura. Deberías considerar deshabilitarlo si no es necesario.
  3. **Restricción de Acceso al Puerto 445**: Asegura que el puerto 445 no esté accesible desde redes no confiables, como el Internet público.

Esta vulnerabilidad es conocida por haber sido explotada de forma masiva, por lo que la acción inmediata es esencial para proteger la máquina contra posibles ataques.
