# Tratamiento de la TTY

En todos los casos, cuando recibimos una reverse shell vemos que nos toca una shell muy inestable que le das a control + c y se cierra y tenemos que hacer otra vez el mismo procedimiento de recibir una reverse shell para que no pase esto haremos un tratamiento de la tty.&#x20;

El comando **➜** `script /dev/null -c bash` inicia una nueva shell de bash en una sesión de script que se redirige al **/dev/null**, lo que significa que la salida del script no se guarda en ningún archivo y simplemente se descarta.

<pre class="language-bash"><code class="lang-bash"><strong>www-data@xxx:/$ script /dev/null -c bash
</strong></code></pre>

Luego hacemos un **Control + Z** para salir-nos y se quede en segundo plano

Ahora resetearemos la configuración de la shell que dejamos en segundo plano indicando **reset xterm**

```bash
> stty raw -echo; fg
[1]  + continued  nc -nlvp 4444
                reset xterm
```

Exportamos las variables de entorno **TERM** y **SHELL**

* `export TERM=xterm` **➜** Debemos hacer esto porque a pesar de haberle indicado que queríamos una **xterm** al momento de reiniciarlo, la variable de entorno **TERM** vale **dump** (Se usa esta variable para poder usar los atajos de teclado).
* `export SHELL=bash` **➜** Para que nuestra shell sea una bash.

```bash
www-data@xxx:/$ export TERM=xterm
www-data@xxx:/$ export SHELL=bash
```

Ya con esto hecho tendríamos una **TTY** full interactiva, pero falta una cosa para que sea lo más cómoda posible, setear las filas y columnas, esto sirve para poder ocupar toda nuestra pantalla, ya que en este momento solo podemos usar una porción de esta.

Vemos el tamaño de columnas y filas de nuestra shell en una terminal aparte nuestra

```bash
> stty size
20 102
```

Y en la reverse shell seteamos las filas y columnas

```bash
www-data@xxx:/$ stty rows 20 columns 102 # (en mi caso son 20 filas y 103 columnas)
```

Y listo!, ya podemos disfrutar de una shell totalmente interactiva.
