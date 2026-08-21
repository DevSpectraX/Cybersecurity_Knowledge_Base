---
tags: [redes, networking, enrutamiento, gateway, arp, icmp, fundamentos]
---



Cuando un dispositivo necesita enviar datos a otro equipo, lo primero que hace es comprobar si el destino está **dentro de su propia subred** o en una **red externa** (como internet u otra sucursal). Si el destino es externo, el dispositivo no puede entregar el paquete directamente; tiene que delegar la responsabilidad en su **Puerta de Enlace** o **Gateway**.

El enrutamiento es el proceso de reenviar esos paquetes a través de redes interconectadas utilizando la mejor ruta disponible.

## 🚪 1. El Gateway (Puerta de Enlace) y la Tabla de Enrutamiento

El **Gateway** es la interfaz de un router conectada a la red local. Tiene una IP privada dentro del mismo rango que los hosts (por lo general, la primera IP útil, como la `192.168.1.1`). Su función es actuar como la "salida del pueblo" hacia el resto del mundo digital.

Cada sistema operativo (Linux, Windows, macOS) mantiene una **Tabla de Enrutamiento** interna en su pila de red para decidir a dónde enviar cada paquete.

### Datos Reales: Consultar la tabla en tu sistema

Tanto en la administración de servidores como en tareas de post-explotación en ciberseguridad, auditar las rutas es obligatorio para entender hacia dónde puede comunicarse una máquina comprometida.

- **En Linux:** Ejecuta `ip route` o `route -n`

- **En Windows:** Ejecuta `route print` o `Get-NetRoute` en PowerShell


Una salida real de `ip route` en Linux se ve así:

Bash

```
default via 192.168.1.1 dev eth0 proto dhcp src 192.168.1.50 metric 100 
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.50 metric 100
```

#### 🛠️ Análisis de la salida:

1. **Línea 2 (`192.168.1.0/24...`):** Le dice al sistema operativo: _"Cualquier tráfico destinado a IPs que empiecen por `192.168.1.X` se envía directamente al cable físico (`scope link`), porque están en mi propia red local"_.

2. **Línea 1 (`default via 192.168.1.1...`):** Es la **Ruta por Defecto** (asociada a la IP `0.0.0.0` en modo conceptual). Dice: _"Si la IP de destino no pertenece a mi red local (por ejemplo, los servidores de Google `8.8.8.8`), escupelo a través de la interfaz `eth0` hacia la IP `192.168.1.1` (mi Gateway) para que él se busque la vida"_.


## 🤝 2. Protocolo ARP (Address Resolution Protocol)

Las IPs sirven para enrutar paquetes a nivel global (Capa 3), pero en la red local (Capa 2), las tarjetas de red solo se entienden mediante **Direcciones MAC**. Cuando tu máquina sabe la IP de destino (o la IP del Gateway), necesita averiguar cuál es su MAC física para poder construir la **Trama**. Aquí entra **ARP**.

### El proceso ARP:

1. Tu máquina envía una petición **ARP Request** en modo _Broadcast_ (a todos los equipos de la red): _"¿Quién tiene la IP 192.168.1.1? Dile a 192.168.1.50 cuál es tu MAC"_.

2. El Gateway escucha la petición y responde con un **ARP Reply** en modo _Unicast_ (directo a ti): _"Yo tengo esa IP, mi MAC es `00:11:22:33:44:55`"_.

3. Tu máquina guarda esta relación en su **Caché ARP** para no tener que preguntar todo el tiempo.


### 💀 Enfoque de Ciberseguridad: ARP Spoofing (Envenenamiento ARP)

El protocolo ARP fue diseñado en una época donde se confiaba en todos los nodos de la red; **no tiene autenticación**.

Un atacante dentro de la red local puede enviar respuestas _ARP Reply_ falsas y constantes al Gateway diciéndole: _"Yo soy la IP del usuario legítimo"_, y al usuario legítimo diciéndole: _"Yo soy el Gateway"_. Al hacer esto, todo el tráfico pasa por la máquina del atacante antes de ir al router. Esto se conoce como un ataque **Man-in-the-Middle (MitM)**.

- **Comando para ver tu tabla ARP real:** `arp -a` (tanto en Windows como en Linux). Si ves dos IPs distintas con la misma dirección MAC física, es un indicador crítico de que estás sufriendo un ataque de envenenamiento ARP.


## 🛰️ 3. Protocolo ICMP (Internet Control Message Protocol)

ICMP es un protocolo de la capa de Internet utilizado por los dispositivos de red para enviar mensajes de diagnóstico, control y error. No transporta datos de usuario; transporta información sobre el estado de la red.

Los dos mensajes más famosos del mundo son:

- **Echo Request (Tipo 8):** La petición que envías.

- **Echo Reply (Tipo 0):** La respuesta que recibes.


Es la base del comando **`ping`**.

### 💀 Enfoque de Ciberseguridad: ICMP en el Reconocimiento

Durante la fase de reconocimiento de un pentest o laboratorio, lanzar pings a un rango de red te ayuda a descubrir qué máquinas están vivas. Además, el valor **TTL (Time to Live)** de la respuesta ICMP te da pistas inmediatas sobre el sistema operativo del objetivo:

- Un TTL cercano a **64** suele indicar que la máquina remota es **Linux**.

- Un TTL cercano a **128** suele indicar que la máquina remota es **Windows**.


## 🔀 4. Enrutamiento Estático vs Dinámico

Los routers necesitan rellenar sus propias tablas de enrutamiento para saber cómo conectar continentes enteros. Lo hacen de dos formas:

1. **Enrutamiento Estático:** El administrador de red introduce las rutas a mano. Es ultra seguro y eficiente para redes pequeñas, pero no escala bien si la red cambia.

2. **Enrutamiento Dinámico:** Los routers hablan entre sí mediante protocolos automáticos para descubrir redes nuevas y desviar el tráfico si un cable se rompe.
 
	- **RIP / OSPF:** Utilizados para enrutar tráfico _dentro_ de una misma empresa o sistema autónomo (IGP).
 
	- **BGP (Border Gateway Protocol):** El protocolo que interconecta los grandes proveedores de internet del mundo (EGP). **Es el pegamento que mantiene unido a internet.**


_MOC de Referencia:_ [[MOC_Redes_Fundamentos]]