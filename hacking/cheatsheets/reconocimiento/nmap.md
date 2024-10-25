# Nmap

## Nmap y sus diferentes modos de escaneo

**Nmap** es una herramienta de **escaneo de red** gratuita y de código abierto que se utiliza en pruebas de penetración (pentesting) para explorar y auditar redes y sistemas informáticos.

Con Nmap, los profesionales de seguridad pueden identificar los **hosts** conectados a una red, los **servicios** que se están ejecutando en ellos y las **vulnerabilidades** que podrían ser explotadas por un atacante. La herramienta es capaz de detectar una amplia gama de dispositivos, incluyendo enrutadores, servidores web, impresoras, cámaras IP, sistemas operativos y otros dispositivos conectados a una red.

Asimismo, esta herramienta posee una variedad de funciones y características avanzadas que permiten a los profesionales de seguridad adaptar la misma a sus necesidades específicas. Estas incluyen técnicas de escaneo agresivas, capacidades de scripting personalizadas, y un conjunto de herramientas auxiliares que pueden ser utilizadas para obtener información adicional sobre los hosts objetivo.

* Opciones de nmap

```shell
> nmap -help
Nmap 7.92 ( https://nmap.org )
Usage: nmap [Scan Type(s)] [Options] {target specification}
TARGET SPECIFICATION:
  Can pass hostnames, IP addresses, networks, etc.
  Ex: scanme.nmap.org, microsoft.com/24, 192.168.0.1; 10.0.0-255.1-254
  -iL <inputfilename>: Input from list of hosts/networks
  -iR <num hosts>: Choose random targets
  --exclude <host1[,host2][,host3],...>: Exclude hosts/networks
  --excludefile <exclude_file>: Exclude list from file
HOST DISCOVERY:
  -sL: List Scan - simply list targets to scan
  -sn: Ping Scan - disable port scan
  -Pn: Treat all hosts as online -- skip host discovery
  -PS/PA/PU/PY[portlist]: TCP SYN/ACK, UDP or SCTP discovery to given ports
  -PE/PP/PM: ICMP echo, timestamp, and netmask request discovery probes
  -PO[protocol list]: IP Protocol Ping
  -n/-R: Never do DNS resolution/Always resolve [default: sometimes]
  --dns-servers <serv1[,serv2],...>: Specify custom DNS servers
  --system-dns: Use OS's DNS resolver
  --traceroute: Trace hop path to each host
SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
  -sU: UDP Scan
  -sN/sF/sX: TCP Null, FIN, and Xmas scans
  --scanflags <flags>: Customize TCP scan flags
  -sI <zombie host[:probeport]>: Idle scan
  -sY/sZ: SCTP INIT/COOKIE-ECHO scans
  -sO: IP protocol scan
  -b <FTP relay host>: FTP bounce scan
PORT SPECIFICATION AND SCAN ORDER:
  -p <port ranges>: Only scan specified ports
    Ex: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9
  --exclude-ports <port ranges>: Exclude the specified ports from scanning
  -F: Fast mode - Scan fewer ports than the default scan
  -r: Scan ports consecutively - don't randomize
  --top-ports <number>: Scan <number> most common ports
  --port-ratio <ratio>: Scan ports more common than <ratio>
SERVICE/VERSION DETECTION:
  -sV: Probe open ports to determine service/version info
  --version-intensity <level>: Set from 0 (light) to 9 (try all probes)
  --version-light: Limit to most likely probes (intensity 2)
  --version-all: Try every single probe (intensity 9)
  --version-trace: Show detailed version scan activity (for debugging)
SCRIPT SCAN:
  -sC: equivalent to --script=default
  --script=<Lua scripts>: <Lua scripts> is a comma separated list of
           directories, script-files or script-categories
  --script-args=<n1=v1,[n2=v2,...]>: provide arguments to scripts
  --script-args-file=filename: provide NSE script args in a file
  --script-trace: Show all data sent and received
  --script-updatedb: Update the script database.
  --script-help=<Lua scripts>: Show help about scripts.
           <Lua scripts> is a comma-separated list of script-files or
           script-categories.
OS DETECTION:
  -O: Enable OS detection
  --osscan-limit: Limit OS detection to promising targets
  --osscan-guess: Guess OS more aggressively
TIMING AND PERFORMANCE:
  Options which take <time> are in seconds, or append 'ms' (milliseconds),
  's' (seconds), 'm' (minutes), or 'h' (hours) to the value (e.g. 30m).
  -T<0-5>: Set timing template (higher is faster)
  --min-hostgroup/max-hostgroup <size>: Parallel host scan group sizes
  --min-parallelism/max-parallelism <numprobes>: Probe parallelization
  --min-rtt-timeout/max-rtt-timeout/initial-rtt-timeout <time>: Specifies
      probe round trip time.
  --max-retries <tries>: Caps number of port scan probe retransmissions.
  --host-timeout <time>: Give up on target after this long
  --scan-delay/--max-scan-delay <time>: Adjust delay between probes
  --min-rate <number>: Send packets no slower than <number> per second
  --max-rate <number>: Send packets no faster than <number> per second
FIREWALL/IDS EVASION AND SPOOFING:
  -f; --mtu <val>: fragment packets (optionally w/given MTU)
  -D <decoy1,decoy2[,ME],...>: Cloak a scan with decoys
  -S <IP_Address>: Spoof source address
  -e <iface>: Use specified interface
  -g/--source-port <portnum>: Use given port number
  --proxies <url1,[url2],...>: Relay connections through HTTP/SOCKS4 proxies
  --data <hex string>: Append a custom payload to sent packets
  --data-string <string>: Append a custom ASCII string to sent packets
  --data-length <num>: Append random data to sent packets
  --ip-options <options>: Send packets with specified ip options
  --ttl <val>: Set IP time-to-live field
  --spoof-mac <mac address/prefix/vendor name>: Spoof your MAC address
  --badsum: Send packets with a bogus TCP/UDP/SCTP checksum
OUTPUT:
  -oN/-oX/-oS/-oG <file>: Output scan in normal, XML, s|<rIpt kIddi3,
     and Grepable format, respectively, to the given filename.
  -oA <basename>: Output in the three major formats at once
  -v: Increase verbosity level (use -vv or more for greater effect)
  -d: Increase debugging level (use -dd or more for greater effect)
  --reason: Display the reason a port is in a particular state
  --open: Only show open (or possibly open) ports
  --packet-trace: Show all packets sent and received
  --iflist: Print host interfaces and routes (for debugging)
  --append-output: Append to rather than clobber specified output files
  --resume <filename>: Resume an aborted scan
  --noninteractive: Disable runtime interactions via keyboard
  --stylesheet <path/URL>: XSL stylesheet to transform XML output to HTML
  --webxml: Reference stylesheet from Nmap.Org for more portable XML
  --no-stylesheet: Prevent associating of XSL stylesheet w/XML output
MISC:
  -6: Enable IPv6 scanning
  -A: Enable OS detection, version detection, script scanning, and traceroute
  --datadir <dirname>: Specify custom Nmap data file location
  --send-eth/--send-ip: Send using raw ethernet frames or IP packets
  --privileged: Assume that the user is fully privileged
  --unprivileged: Assume the user lacks raw socket privileges
  -V: Print version number
  -h: Print this help summary page.
EXAMPLES:
  nmap -v -A scanme.nmap.org
  nmap -v -sn 192.168.0.0/16 10.0.0.0/8
  nmap -v -iR 10000 -Pn -p 80
```

### Escaneos básicos

* Escaneo para escanear todos los equipos de la red indicada y con expresiones regulares filtrar por lo interesante

```shell
> nmap -sn <rango_de_red>/CIDR | grep -oP '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}'
# Ejemplo
> nmap -sn 192.168.1.0/24 | grep -oP '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}'
```

* Escaneo para escanear todo el rango de **65535** puertos abiertos de la **victima** por el protocolo **TCP**

```shell
> nmap -p- --open -vvv -n -Pn <ip-victima>
```

* Escaneo para escanear todo el rango de **65535** puertos abiertos de la **victima** por el protocolo **UDP**

```shell
> nmap -p- --open -sU -vvv -n -Pn <ip-victima>
```

* Escaneo para escanear versiones de servicios pasados por el parámetro **-p**

```shell
> nmap -p<puertos> -sV -vvv -n -Pn <ip-victima>
# Ejemplo para los puertos ftp, ssh y http
> nmap -p21,22,80 -sV -vvv -n -Pn 10.10.10.55
```

## Técnicas de evasión de Firewalls (MTU, Data Length, Source Port, Decoy, etc.)

Cuando se realizan pruebas de penetración, uno de los mayores desafíos es evadir la detección de los **Firewalls**, que son diseñados para proteger las redes y sistemas de posibles amenazas. Para superar este obstáculo, Nmap ofrece una variedad de técnicas de evasión que permiten a los profesionales de seguridad realizar escaneos sigilosos y evitar así la detección de los mismos.

Algunos de los parámetros vistos en esta clase son los siguientes:

* **MTU (–mtu)**: La técnica de evasión de **MTU** o “**Maximum Transmission Unit**” implica ajustar el tamaño de los paquetes que se envían para evitar la detección por parte del Firewall. Nmap permite configurar manualmente el tamaño máximo de los paquetes para garantizar que sean lo suficientemente pequeños para pasar por el Firewall sin ser detectados.

```shell
> nmap -p<puertos> --mtu <numero multiplo de 8> <ip-victima>
# Ejemplo con el puerto 22
> nmap -p22 --mtu 8 192.168.1.10
```

* **Data Length (–data-length)**: Esta técnica se basa en ajustar la **longitud de los datos** enviados para que sean lo suficientemente cortos como para pasar por el Firewall sin ser detectados. Nmap permite a los usuarios configurar manualmente la longitud de los datos enviados para que sean lo suficientemente pequeños para evadir la detección del Firewall.

```shell
> nmap -p<puerto> <ip-victima> --data-length <numero>
# Ejemplo con el puerto ssh
> nmap -p22 10.10.12.5 --data-length 21
```

* **Source Port (–source-port)**: Esta técnica consiste en configurar manualmente el número de **puerto de origen** de los paquetes enviados para evitar la detección por parte del Firewall. Nmap permite a los usuarios especificar manualmente un puerto de origen aleatorio o un puerto específico para evadir la detección del Firewall.

```shell
> nmap -p<puerto> <ip-victima> --open -T5 -v -n --source-port <puerto>
# Ejemplo con el puerto ssh
> nmap -p22 10.10.11.2 --open -T5 -v -n --source-port 53
```

* **Decoy (-D)**: Esta técnica de evasión en Nmap permite al usuario enviar **paquetes falsos** a la red para confundir a los sistemas de detección de intrusos y evitar la detección del Firewall. El comando **-D** permite al usuario enviar paquetes falsos junto con los paquetes reales de escaneo para ocultar su actividad.

```shell
> nmap -p<puertos> <ip-victima> -D <ip-falsa>
# Ejemplo con el puerto 22
> nmap -p22 192.168.1.5 -D 192.168.1.17
```

* **Fragmented (-f)**: Esta técnica se basa en **fragmentar los paquetes** enviados para que el Firewall no pueda reconocer el tráfico como un escaneo. La opción **-f** en Nmap permite fragmentar los paquetes y enviarlos por separado para evitar la detección del Firewall.

```shell
> nmap -p<puertos> <ip-victima> -f
# Ejemplo con el puerto 22
> nmap -p22 192.168.1.5 -f
```

* **Spoof-Mac (–spoof-mac)**: Esta técnica de evasión se basa en **cambiar la dirección MAC** del paquete para evitar la detección del Firewall. Nmap permite al usuario configurar manualmente la dirección MAC para evitar ser detectado por el Firewall.

```shell
> nmap -p<puerto> -Pn <ip-victima> --spoof-mac <mac>
# Ejemplo con el puerto 22
> nmap -p22 -Pn 10.10.11.12 --spoof-mac Dell
> nmap -p22 -Pn 10.10.11.13 --spoof-mac 24:5e:be:3d:5d:fa
```

* **Stealth Scan (-sS)**: Esta técnica es una de las más utilizadas para realizar escaneos sigilosos y evitar la detección del Firewall. El comando **-sS** permite a los usuarios realizar un escaneo de tipo **SYN** **sin establecer una conexión completa**, lo que permite evitar la detección del Firewall.

```shell
> nmap -p<puertos> --open -sS -n -v <ip-victima>
# Ejemplo con el puerto ssh
> nmap -p22 --open -sS -n -v 10.10.11.10
```

* **min-rate (–min-rate)**: Esta técnica permite al usuario **controlar la velocidad de los paquetes** enviados para evitar la detección del Firewall. El comando **–min-rate** permite al usuario reducir la velocidad de los paquetes enviados para evitar ser detectado por el Firewall.

```shell
> nmap -p<puertos> --open --min-rate <numero-paquetes> -sS -n -v -Pn <ip-victima>
# Ejemplo con el puerto ssh
> nmap -p<22 --open --min-rate 5000 -sS -n -v -Pn 10.10.11.10
```

Es importante destacar que, además de las técnicas de evasión mencionadas anteriormente, existen muchas otras opciones en Nmap que pueden ser utilizadas para realizar pruebas de penetración efectivas y evadir la detección del Firewall. Sin embargo, las técnicas que hemos mencionado son algunas de las más populares y ampliamente utilizadas por los profesionales de seguridad para superar los obstáculos que presentan los Firewalls en la realización de pruebas de penetración.

## Uso de scripts y categorías en nmap para aplicar reconocimiento

Una de las características más poderosas de **Nmap** es su capacidad para automatizar tareas utilizando **scripts personalizados**. Los scripts de Nmap permiten a los profesionales de seguridad automatizar las tareas de reconocimiento y descubrimiento en la red, además de obtener información valiosa sobre los sistemas y servicios que se están ejecutando en ellos. El parámetro **–script** de Nmap permite al usuario seleccionar un conjunto de scripts para ejecutar en un objetivo de escaneo específico.

Existen diferentes categorías de scripts disponibles en Nmap, cada una diseñada para realizar una tarea específica. Algunas de las categorías más comunes incluyen:

Para ver todas las categorias.

```shell
locate .nse | xargs grep "categories" | grep -oP '".*?"' | sort -u
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

* **default**: Esta es la categoría predeterminada en Nmap, que incluye una gran cantidad de scripts de reconocimiento básicos y útiles para la mayoría de los escaneos.

```shell
> nmap -p<puertos> --script="default" <ip-victima>
# Ejemplo sobre el puerto ssh
> nmap -p22 --script="default" 10.10.11.10
```

* **discovery**: Esta categoría se enfoca en descubrir información sobre la red, como la detección de hosts y dispositivos activos, y la resolución de nombres de dominio.

```shell
> nmap -p<puertos> --script="discovery" <ip-victima>
# Ejemplo sobre el puerto ssh
> nmap -p22 --script="discovery" 10.10.11.10
```

* **safe**: Esta categoría incluye scripts que son considerados seguros y que no realizan actividades invasivas que puedan desencadenar una alerta de seguridad en la red.

```shell
> nmap -p<puertos> --script="safe" <ip-victima>
# Ejemplo sobre el puerto ssh
> nmap -p22 --script="default" 10.10.11.10
```

* **intrusive**: Esta categoría incluye scripts más invasivos que pueden ser detectados fácilmente por un sistema de detección de intrusos o un Firewall, pero que pueden proporcionar información valiosa sobre vulnerabilidades y debilidades en la red.

```shell
> nmap -p<puertos> --script="intrusive" <ip-victima>
# Ejemplo sobre el puerto ssh
> nmap -p22 --script="default" 10.10.11.10
```

* **vuln**: Esta categoría se enfoca específicamente en la detección de vulnerabilidades y debilidades en los sistemas y servicios que se están ejecutando en la red.

```shell
> nmap -p<puertos> --script="vuln" <ip-victima>
# Ejemplo sobre el puerto ssh
> nmap -p22 --script="default" 10.10.11.10
```

* **http-enum**: Este script básico de reconocimiento actua como un fuzzer para hacer fuerza bruta para encontrar directorios, rutas.

```shell
> nmap -p<puertos-web> --script http-enum <ip-victima>
# Ejemplo sobre el puerto http
> nmap -p80 --script http-enum 10.10.11.10
```

En conclusión, el uso de scripts y categorías en Nmap es una forma efectiva de automatizar tareas de reconocimiento y descubrimiento en la red. El parámetro **–script** permite al usuario seleccionar un conjunto de scripts personalizados para ejecutar en un objetivo de escaneo específico, mientras que las diferentes categorías disponibles en Nmap se enfocan en realizar tareas específicas para obtener información valiosa sobre la red.

## Creación de tus propios scripts en Lua para nmap

Nmap permite a los profesionales de seguridad personalizar y extender sus capacidades mediante la creación de scripts personalizados en el lenguaje de programación **Lua**. Lua es un lenguaje de scripting simple, flexible y poderoso que es fácil de aprender y de usar para cualquier persona interesada en crear scripts personalizados para Nmap.

Para utilizar Lua como un script personalizado en Nmap, es necesario tener conocimientos básicos del lenguaje de programación Lua y comprender la estructura básica que debe tener el script. La estructura básica de un script de Lua en Nmap incluye la definición de una tabla, que contiene diferentes campos y valores que describen la funcionalidad del script.

Los campos más comunes que se definen en la tabla de un script de Lua en Nmap incluyen:

* **description**: Este campo se utiliza para proporcionar una descripción corta del script y su funcionalidad.
* **categories**: Este campo se utiliza para especificar las categorías a las que pertenece el script, como descubrimiento, explotación, enumeración, etc.
* **author**: Este campo se utiliza para identificar al autor del script.
* **license**: Este campo se utiliza para especificar los términos de la licencia bajo la cual se distribuye el script.
* **dependencies**: Este campo se utiliza para especificar cualquier dependencia de biblioteca o software que requiera el script para funcionar correctamente.
* **actions**: Este campo se utiliza para definir la funcionalidad específica del script, como la realización de un escaneo de puertos, la detección de vulnerabilidades, etc.

Una vez que se ha creado un script de Lua personalizado en Nmap, se puede invocar utilizando el parámetro **–script** y el nombre del archivo del script. Con la creación de scripts personalizados en Lua, es posible personalizar aún más las capacidades de Nmap y obtener información valiosa sobre los sistemas y servicios en la red.

* Script que enumera y reporta puertos abiertos por TCP

```lua
-- HEAD --
description = [[
Script de ejemplo que enumera y reporta puertos abiertos por TCP
]]

-- RULE --

portrule = function(host, port)
	return port.protocol == "tcp"
		and port.state == "open"
end
-- ACTION --

action = "function(host, port)"
	return "This port is open!"
end
```

* Para lanzar el script

```shell
> nmap -p<puertos> --script <archivo> <ip-victima>
# Ejemplo con el puerto 22
> nmap -p22 --script archivo.nse 192.168.1.39
```
