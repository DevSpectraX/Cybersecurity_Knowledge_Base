---
tags: [redes, networking, transporte, tcp, handshake, flags, fundamentos]
---

El **Apretón de Manos de Tres Vías** (o _Three-Way Handshake_) es el mecanismo exacto que utiliza el protocolo **TCP** en la capa de Transporte para establecer una conexión fiable y orientada a sesión entre dos dispositivos antes de que se intercambie cualquier dato de aplicación.

En el mundo de la ciberseguridad, este proceso es el pilar fundamental para comprender los escaneos de puertos (como el sigiloso _SYN Stealth Scan_), los ataques de denegación de servicio (DoS) por inundación y el funcionamiento de los firewalls con estado (_Stateful Firewalls_).

## 🚩 1. Las Banderas TCP (Flags)

Para entender la coreografía del saludo, primero debemos conocer los controles que viajan en la cabecera de cada segmento TCP. Estos controles se llaman **Flags** (Banderas) y son bits individuales que se ponen en "1" (activado) o "0" (desactivado) para indicarle al receptor el propósito exacto del paquete:

- **`SYN` (Synchronize):** Se activa únicamente para iniciar o sincronizar una nueva conexión.
    
- **`ACK` (Acknowledge):** Indica que el número de acuse de recibo es válido (confirma que se recibió el paquete anterior).
    
- **`FIN` (Finish):** Se activa para solicitar el cierre de la conexión de forma ordenada y amistosa.
    
- **`RST` (Reset):** Cancela o aborta la conexión de forma inmediata y abrupta debido a un error o porque el puerto está cerrado.
    
- **`PSH` (Push):** Le dice al sistema operativo receptor que envíe los datos directamente a la aplicación sin esperar a llenar el búfer de memoria.
    
- **`URG` (Urgent):** Indica que los datos contenidos en el segmento son prioritarios y deben procesarse de inmediato.
    

## 🤝 2. La Coreografía del Saludo (Paso a Paso)

Imaginemos un caso real donde tu máquina cliente (`192.168.1.50`) quiere abrir una página web alojada en un servidor local (`192.168.1.100`) a través del puerto TCP `80`.

### Paso 1: El Cliente inicia (`SYN`)

El cliente selecciona un **Número de Secuencia Inicial** aleatorio (ej: $Seq = 1000$) y envía un segmento con la bandera **`SYN`** activada.

- **Mensaje:** _"Hola, quiero abrir una conexión contigo en tu puerto 80. Mi número de secuencia base es el 1000"_.
    
- **Estado del Cliente:** Pasa a `SYN-SENT`.
    

### Paso 2: El Servidor responde (`SYN-ACK`)

El servidor recibe el paquete. Si el puerto `80` está abierto y escuchando, acepta la petición. Elige su propio número de secuencia aleatorio (ej: $Seq = 4000$) e incrementa en 1 el número de secuencia del cliente para confirmar que lo recibió ($Ack = 1001$). Envía un segmento con las banderas **`SYN`** y **`ACK`** activadas.

- **Mensaje:** _"Hola. Recibí tu petición (confirmo el 1001). Yo también quiero sincronizar contigo; mi número de secuencia base es el 4000"_.
    
- **Estado del Servidor:** Pasa a `SYN-RECEIVED`.
    

### Paso 3: El Cliente confirma (`ACK`)

El cliente recibe la respuesta del servidor. Para cerrar el trato, incrementa en 1 el número de secuencia del servidor ($Ack = 4001$) y envía un último segmento con la bandera **`ACK`** activada.

- **Mensaje:** _"¡Perfecto! Confirmado tu número 4001. El canal está abierto, procedo a enviarte los datos reales"_.
    
- **Estado de ambos:** Pasan a `ESTABLISHED`. A partir de este microsegundo, empieza a transmitirse el tráfico web (`HTTP`).
    

## 💀 Enfoque de Ciberseguridad: Ataques y Escaneos Relacionados

Manipular este saludo es una de las técnicas de hacking de red más comunes y efectivas del mundo:

### 1. Escaneo Síncrono Sigiloso (SYN Stealth Scan o Half-Open)

Es el escaneo por defecto de herramientas como Nmap (`nmap -sS`). Su objetivo es descubrir si un puerto está abierto sin llegar a establecer una conexión completa, evitando así dejar registros (logs) en la aplicación del servidor.

1. El atacante envía un **`SYN`**.
    
2. Si el servidor responde con **`SYN-ACK`**, el atacante ya sabe que el puerto está **abierto**.
    
3. En lugar de enviar el último `ACK` para cerrar el saludo, el atacante envía un **`RST` (Reset)** para romper la conexión de golpe. La aplicación web del servidor nunca se entera de que alguien preguntó.
    

### 2. Ataque de Inundación SYN (SYN Flood DoS)

Un ataque de denegación de servicio clásico que abusa de la memoria del sistema operativo del objetivo.

- El atacante envía miles de paquetes **`SYN`** por segundo utilizando IPs de origen falsas (Spoofing).
    
- El servidor responde con un **`SYN-ACK`** para cada petición y reserva un espacio en su memoria RAM esperando el último `ACK` de confirmación (dejando la conexión en estado "medio abierta" o _Half-Open_).
    
- Como las IPs de origen eran falsas, el último `ACK` nunca llega. El servidor agota rápidamente sus recursos de memoria guardando estas conexiones fantasma y colapsa, dejando de atender a usuarios legítimos.
    

### 3. El Cierre de Conexión Ordenado (The Four-Way Handshake)

Aunque para abrir la conexión se necesitan 3 pasos, para cerrarla de forma limpia se necesitan **4 pasos** utilizando la bandera **`FIN`**:

1. Cliente envía `FIN` -> _(Ya no tengo más datos que enviar)_.
    
2. Servidor responde `ACK` -> _(Entendido, cierro tu canal de envío)_.
    
3. Servidor envía su propio `FIN` -> _(Yo tampoco tengo más que decirte)_.
    
4. Cliente responde `ACK` -> _(Entendido, conexión totalmente cerrada)_.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Redes]]