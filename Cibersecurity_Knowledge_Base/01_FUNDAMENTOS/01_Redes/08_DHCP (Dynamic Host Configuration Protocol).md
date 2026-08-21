---
tags: [redes, networking, dhcp, dora, automatizacion, fundamentos]
---
# DHCP (Dynamic Host Configuration Protocol)

El **DHCP** (Protocolo de Configuración Dinámica de Anfitriones) es un servicio esencial de la capa de Aplicación que funciona sobre el protocolo de transporte **UDP (Puertos 67 para el servidor y 68 para el cliente)**. Su único propósito es automatizar por completo la configuración de red de los dispositivos que se conectan a una infraestructura, asignándoles de forma dinámica parámetros críticos como la dirección IP, la máscara de subred, la puerta de enlace por defecto (Gateway) y los servidores DNS.

Sin DHCP, los administradores de sistemas tendrían que configurar manualmente cada ordenador, smartphone o servidor de la empresa, provocando colisiones de IP por errores humanos. En ciberseguridad, es un protocolo crítico debido a su total falta de autenticación por defecto.

## 🤝 1. El Proceso de Intercambio DORA (Paso a Paso)

Cuando un dispositivo se enciende o se conecta a una red (por cable o Wi-Fi) no tiene dirección IP. Para comunicarse con el servidor DHCP de la red local, el cliente inicia una coreografía de 4 pasos conocida por el acrónimo **DORA**:

### 1. **D**iscover (Descubrimiento - Cliente a Todos)

Como el cliente no sabe quién es el servidor DHCP ni qué IP tiene la red, lanza un paquete de difusión masiva (**Broadcast**) a la capa de red (`255.255.255.255`) y a la capa de enlace (`FF:FF:FF:FF:FF:FF`).

- **Mensaje:** _"¡Hola! Acabo de llegar a la red y mi dirección MAC física es `00:0C:29:AA:BB:CC`. ¿Hay algún servidor DHCP ahí fuera que pueda darme una configuración?"_.
    

### 2. **O**ffer (Ofrecimiento - Servidor a Cliente)

El servidor (o servidores) DHCP de la red local escucha la petición. Revisa su base de datos de IPs disponibles (llamada _Address Pool_) y reserva una dirección para el cliente. Responde habitualmente mediante **Unicast** (directo a la MAC del cliente) o _Broadcast_ dependiendo de la implementación.

- **Mensaje:** _"Hola `00:0C:29:AA:BB:CC`. Soy el servidor DHCP (`192.168.1.1`). He visto tu petición y te ofrezco la IP `192.168.1.75` con la máscara `255.255.255.0` y el Gateway `192.168.1.1` durante un tiempo de concesión de 24 horas"_.
    

### 3. **R**equest (Petición - Cliente a Todos)

El cliente recibe la oferta (si recibe varias, suele quedarse con la primera que llegue). Vuelve a enviar un paquete en modo **Broadcast** para avisar formalmente de su elección a todos los servidores de la sala.

- **Mensaje:** _"¡Acepto la oferta! Me quedo con la IP `192.168.1.75` que me ha ofrecido el servidor `192.168.1.1`. Por favor, confírmamelo formalmente (y que el resto de servidores liberen las otras IPs que me hubieran ofrecido)"_.
    

### 4. **A**cknowledge (Acuse de Recibo - Servidor a Cliente)

El servidor DHCP recibe la confirmación del cliente, asocia oficialmente esa dirección IP a la dirección MAC del cliente en su tabla de concesiones (_leases_) y le envía el último paquete de confirmación.

- **Mensaje:** _"¡Perfecto! Todo anotado en mi base de datos. La IP `192.168.1.75` es oficialmente tuya a partir de ahora. Puedes empezar a usar la red"_.
    

## ⏱️ 2. El Concepto de Concesión (Lease Time)

Las IPs asignadas por DHCP no son propiedad del dispositivo para siempre; se alquilan por un periodo de tiempo determinado llamado **Lease Time** (Concesión).

- **El proceso de renovación:** Cuando se cumple el **50%** del tiempo de alquiler (T1), el cliente envía automáticamente un paquete `DHCP Request` directamente al servidor para solicitar una prórroga. Si el servidor responde con un `DHCP ACK`, el temporizador se reinicia.
    
- Si el servidor no responde (por estar apagado o caído), el cliente puede seguir usando la IP hasta que se cumpla el **87.5%** del tiempo (T2), momento en el cual lanzará un `DHCP Request` en modo _Broadcast_ a la desesperada para que _cualquier_ servidor DHCP disponible en la red le valide la IP. Si el tiempo expira del todo, el cliente pierde la IP y debe reiniciar el proceso DORA desde cero.
    

> 🛠️ **El misterio de la IP `169.254.X.X` (APIPA):** Si alguna vez ejecutas `ipconfig` (Windows) o `ip a` (Linux) y ves una IP en el rango `169.254.0.0/16`, significa que tu sistema operativo ha enviado un paquete _DHCP Discover_ pero **nadie le ha respondido**. Ante el silencio, el protocolo APIPA le asigna esa IP de autoconfiguración para que al menos pueda hablar con otras máquinas locales en su misma situación precaria.

## 💀 Enfoque de Ciberseguridad: Vulnerabilidades del DHCP

Dado que DHCP fue diseñado para entornos de red de confianza y los paquetes de descubrimiento iniciales se envían a ciegas (a todo el mundo por broadcast), es sumamente fácil de manipular por un atacante:

### 1. Servidores DHCP Falsos (Rogue DHCP Server)

Un atacante dentro de la red local levanta su propio servidor DHCP malicioso (por ejemplo, usando herramientas como _Yersinia_ o scripts personalizados).

- Cuando una nueva víctima se conecta a la red y lanza su `DHCP Discover`, el atacante compite en velocidad contra el servidor DHCP legítimo.
    
- Si el atacante responde más rápido, puede asignarle a la víctima una configuración de red maliciosa donde la IP del **Gateway** o del **Servidor DNS** sea la propia IP del atacante.
    
- **Resultado:** La víctima navegará con normalidad, pero todo su tráfico pasará directamente por los ojos del atacante, ejecutando un ataque **Man-in-the-Middle (MitM)** impecable a nivel de red sin necesidad de envenenar la tabla ARP.
    

### 2. Agotamiento de IPs DHCP (DHCP Starvation Attack)

Un ataque de denegación de servicio (DoS) dirigido a consumir todos los recursos del servidor DHCP legítimo.

- El atacante inunda la red enviando miles de peticiones `DHCP Discover` falsas por segundo, **falsificando una dirección MAC de origen distinta en cada paquete**.
    
- El servidor DHCP, creyendo que son miles de ordenadores nuevos que acaban de conectarse a la oficina, responde asignando y reservando una IP real de su _pool_ para cada MAC falsa.
    
- En cuestión de segundos, el rango de IPs del servidor DHCP se agota por completo. Cuando un empleado legítimo llega a la oficina y enciende su ordenador, el servidor no tiene IPs que ofrecerle y el empleado se queda sin conexión a la red. (Este ataque suele combinarse con el _Rogue DHCP Server_ anterior para forzar a las víctimas a usar el servidor del atacante).
    

### 🛡️ Defensas en el Mundo Real: DHCP Snooping

Para evitar estos ataques en redes corporativas, los administradores configuran una tecnología de seguridad en los **Switches** (Capa 2) llamada **DHCP Snooping**. Esta tecnología clasifica los puertos del switch en:

- **Puertos de Confianza (Trusted):** Donde está conectado físicamente el servidor DHCP legítimo. Solo estos puertos pueden enviar paquetes de respuesta como `DHCP Offer` o `DHCP ACK`.
    
- **Puertos Inseguros (Untrusted):** Donde se conectan los usuarios comunes. Si el switch detecta que desde el puerto de un usuario común intenta salir un paquete `DHCP Offer`, bloquea inmediatamente el puerto por seguridad, neutralizando al atacante.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Redes]]