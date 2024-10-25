# Escaneo de Puertos bajo el Protocolo UDP

Escanear puertos sobre el protocolo UDP con la herramienta nmap utilizando como objetivo la máquina chain de [vulnyx](https://vulnyx.com/#cha).

* Primero buscaremos la máquina haciendo reconocimiento de la red:

```bash
└─# arp-scan -I eth0 --localnet   
Interface: eth0, type: EN10MB, MAC: 08:00:27:ba:28:e1, IPv4: 192.168.1.18
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	a8:9a:93:66:15:50	(Unknown)
192.168.1.15	f4:7b:09:52:3d:48	(Unknown)
192.168.1.120	08:00:27:20:9b:22	(Unknown)
192.168.1.13	fe:d5:2d:56:5a:08	(Unknown: locally administered)
192.168.1.11	d8:1e:dd:32:a2:9d	(Unknown)

5 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.328 seconds (109.97 hosts/sec). 5 responded
```

* Escanear puertos sobre el protocolo UDP con el parametro -sU en la herramienta nmap:

```bash
└─# nmap -sU --top-ports 200 --min-rate=5000 -Pn 192.168.1.120 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-16 17:48 CEST
Nmap scan report for chain.home (192.168.1.120)
Host is up (0.0031s latency).
Not shown: 193 open|filtered udp ports (no-response)
PORT      STATE  SERVICE
161/udp   open   snmp
1030/udp  closed iad1
4672/udp  closed rfa
5000/udp  closed upnp
6000/udp  closed X11
34861/udp closed unknown
49200/udp closed unknown
MAC Address: 08:00:27:20:9B:22 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 0.76 seconds
```

Este comando en particular tiene varios componentes:

1. **`-sU`**: Le dice a Nmap que quieres escanear puertos **UDP**. Los puertos UDP son uno de los dos tipos principales de puertos (el otro es TCP). UDP es un protocolo que se usa para cosas como transmisión de video y juegos online porque es más rápido, aunque menos confiable.
2. **`--top-ports 200`**: En lugar de escanear **todos los puertos posibles** (hay 65,535 puertos), Nmap solo buscará los **200 más comunes**. Esto lo hace para ahorrar tiempo.
3. **`--min-rate=5000`**: Nmap intentará enviar **5000 paquetes por segundo**. Esto significa que el escaneo será muy rápido. Si la red o el objetivo no soporta una tasa tan alta, algunos puertos pueden no responder, lo que podría darte resultados incompletos.
4. **`-Pn`**: Normalmente, antes de hacer un escaneo, Nmap verifica si el dispositivo está activo (en línea) haciendo un **ping**. Al usar `-Pn`, Nmap se saltará este paso y simplemente asumirá que el dispositivo está encendido, para ahorrar tiempo.
5. **`<ip-objetivo>`**: Aquí se coloca la **dirección IP del dispositivo que deseas escanear**.

#### ¿Qué hace este comando?

* Escanea 200 puertos UDP más comunes en la dirección IP que le indiques.
* Envía paquetes rápidamente, lo cual puede ayudar a reducir el tiempo total del escaneo.
* No se asegura de que el dispositivo esté activo antes de empezar, simplemente escanea directamente.
