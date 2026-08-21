---
tags: [redes, networking, transporte, tcp, udp, fundamentos]
---

La capa de Transporte (Capa 4 del modelo OSI) tiene una única misión fundamental: recibir los datos de la capa de aplicación y entregarlos de forma óptima a la máquina de destino. Para lograrlo, los sistemas operativos utilizan principalmente dos protocolos que representan filosofías de comunicación totalmente opuestas: **TCP** y **UDP**.

En ciberseguridad, entender la diferencia exacta entre ambos es vital para comprender cómo funcionan los escaneos de puertos (`nmap`), cómo se detectan las intrusiones (IDS/IPS) y cómo se comportan los diferentes tipos de malware.

## 🏗️ 1. TCP (Transmission Control Protocol)

TCP es un protocolo **orientado a conexión, fiable y estructurado**. Su prioridad absoluta es la **integridad de los datos**: prefiere que la comunicación sea ligeramente más lenta antes que perder un solo bit por el camino.

### ⚙️ Características Clave:

- **Establecimiento de sesión:** Antes de enviar datos, el emisor y el receptor deben saludarse formalmente mediante [[06_El Apretón de Manos de Tres Vías (Three-Way Handshake)]]
    
- **Control de flujo y congestión:** Si el emisor envía datos demasiado rápido y satura la memoria del receptor o los routers intermedios, TCP ralentiza la transmisión de forma automática.
    
- **Reordenamiento y Acuses de Recibo (ACKs):** Cada segmento lleva un número de secuencia. Si un trozo de datos llega desordenado, el sistema operativo del receptor lo recoloca en su sitio. Si un fragmento se pierde en el cable, el receptor avisa al emisor para que lo vuelva a enviar.


### 📡 Casos de Uso Reales:

Se utiliza en cualquier servicio donde un error de un solo byte rompería el archivo o la sesión por completo:

- **HTTP/HTTPS (Puertos 80/443):** Una web corrupta no cargaría o fallaría el código script.
    
- **SSH (Puerto 22):** La consola remota perdería caracteres de los comandos ejecutados.
    
- **FTP/SMB (Puertos 21/445):** Un archivo descargado a medias estaría totalmente dañado.
    

## ⚡ 2. UDP (User Datagram Protocol)

UDP es un protocolo **no orientado a conexión, rápido y sin estado**. Su prioridad absoluta es la **velocidad y la baja latencia**.

### ⚙️ Características Clave:

- **Enviar y olvidar (Fire and Forget):** UDP no saluda antes de enviar un paquete ni comprueba si el receptor está encendido o listo para escuchar. Simplemente escupe los datos (llamados _Datagramas_) hacia la red.
    
- **Sin acuses de recibo:** No le importa si los paquetes se pierden, llegan duplicados o llegan desordenados. No retransmite nada.
    
- **Cabecera ultra ligera:** Mientras que la cabecera base de TCP pesa un mínimo de 20 bytes debido a todos sus controles, la cabecera de UDP mide únicamente **8 bytes**. Menos peso significa transmisiones mucho más ágiles.
    

### 📡 Casos de Uso Reales:

Se utiliza en servicios en tiempo real donde la velocidad es crítica y la pérdida de pequeños fragmentos de información es perfectamente tolerable:

- **Streaming de audio/video y VoIP:** Si se pierde un milisegundo de voz en una llamada, se escucha un pequeño chasquido y la llamada sigue; sería peor pausar la voz para retransmitir ese fragmento viejo.
    
- **Videojuegos online:** Es fundamental saber dónde está el rival _ahora mismo_, no hace un segundo.
    
- **DNS (Puerto 53) y DHCP (Puertos 67/68):** Consultas ultra rápidas de una sola pregunta y una sola respuesta.
    

## ⚖️ Tabla Comparativa Técnica

|**Característica**|**TCP**|**UDP**|
|---|---|---|
|**Orientado a conexión**|**Sí** (Requiere saludo previo)|**No** (Envío directo)|
|**Fiabilidad**|**Garantizada** (Retransmite si hay pérdidas)|**No garantizada** (Puede haber pérdidas)|
|**Orden de los datos**|**Garantizado** (Usa números de secuencia)|**No garantizado** (Llegan como caigan)|
|**Velocidad**|Más lento (Por la sobrecarga de control)|**Ultra rápido** (Mínimo procesamiento)|
|**Tamaño de Cabecera**|Mínimo 20 bytes|**Siempre 8 bytes**|
|**Unidad de Datos (PDU)**|**Segmento**|**Datagrama**|

## 💀 Enfoque de Ciberseguridad: Escaneo de Puertos (`nmap`)

El comportamiento tan dispar de estos dos protocolos cambia radicalmente la forma en que un atacante o un auditor descubre qué servicios están expuestos en un servidor:

### 🔍 Escaneo TCP (`nmap -sS` o `-sT`)

Es sumamente fiable y rápido de detectar. Si envías un paquete de saludo (`SYN`) a un puerto TCP:

- Si el puerto está **abierto**, el servidor responderá con un `SYN-ACK`.
    
- Si el puerto está **cerrado**, el servidor responderá con un `RST` (Reset).
    
- Esto permite mapear redes enteras con absoluta certeza en segundos.
    

### 🔍 Escaneo UDP (`nmap -sU`)

Es una pesadilla para los auditores de seguridad porque, como UDP no responde por diseño, es muy difícil saber qué está pasando:

- Si envías un datagrama UDP a un puerto y el puerto está **abierto**, lo normal es que la aplicación reciba el paquete, vea que no tiene sentido y **no responda nada** (silencio absoluto).
    
- Si el puerto está **cerrado**, el sistema operativo del servidor suele enviar de vuelta un paquete ICMP de error diciendo _"Puerto inaccesible"_ (Tipo 3, Código 3).
    
- **El problema:** Si un firewall en el camino bloquea el paquete, `nmap` tampoco recibirá respuesta, por lo que marcará el puerto como `open|filtered` (abierto o filtrado, sin poder asegurar cuál de los dos). Además, los escaneos UDP son extremadamente lentos porque los sistemas operativos limitan por seguridad la cantidad de respuestas ICMP que envían por segundo.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Redes]]