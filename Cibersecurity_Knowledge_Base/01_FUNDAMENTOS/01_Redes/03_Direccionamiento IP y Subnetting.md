---
tags: [redes, networking, ip, subnetting, cidr, fundamentos]
---


El **Direccionamiento IP** es el sistema de identificación lógica utilizado en la capa de Internet (o de Red) para permitir que los dispositivos se localicen y comuniquen entre sí. El **Subnetting** (o subdivisión de redes) es la técnica que consiste en dividir una red física o lógica grande en redes más pequeñas (subredes) para mejorar el rendimiento, la organización y la seguridad.

## 🔢 1. Estructura de una Dirección IPv4

Una dirección IPv4 está compuesta por **32 bits**, divididos en 4 grupos de 8 bits (octetos) separados por puntos. Cada octeto puede tener un valor decimal entre 0 y 255 (ej: `192.168.1.15`).

Toda dirección IP se divide obligatoriamente en dos partes:

1. **Network ID (Identificador de Red):** Indica a qué red pertenece el dispositivo (como el código postal).

2. **Host ID (Identificador de Dispositivo):** Identifica al dispositivo específico dentro de esa red (como el número de casa).


### La Máscara de Subred

Es la que le dice al sistema operativo dónde termina la red y dónde empiezan los hosts. Se representa en formato decimal (`255.255.255.0`) o mediante la notación **CIDR** (`/24`), que simplemente cuenta el número de bits puestos a "1" de izquierda a derecha.

- Una IP `192.168.1.50` con máscara `255.255.255.0` (o `/24`) significa que los primeros 24 bits (`192.168.1`) pertenecen a la **Red**, y el último octeto (`50`) pertenece al **Host**.


## 🧱 2. IPs Públicas, Privadas (RFC 1918) y Direcciones Especiales

Las direcciones IP se clasifican según su ámbito de uso:

- **IPs Públicas:** Son enrutables en todo internet. Son únicas globalmente y las asignan los proveedores de servicios (ISPs).

- **IPs Privadas (RFC 1918):** No son enrutables en internet. Se utilizan para redes locales (LAN) y se pueden reutilizar en diferentes organizaciones. Un router casero o empresarial usa **NAT** para traducir estas IPs privadas en una única IP pública para salir a internet.


### Rangos Privados Estándar:

- **Clase A:** `10.0.0.0` a `10.255.255.255` (Máscara por defecto: `/8`)

- **Clase B:** `172.16.0.0` a `172.31.255.255` (Máscara por defecto: `/12`)

- **Clase C:** `192.168.0.0` a `192.168.255.255` (Máscara por defecto: `/16`)


### Direcciones Especiales que NUNCA debes olvidar:

- **`127.0.0.1` (Loopback o localhost):** Se apunta a la propia máquina local. Útil para pruebas de servicios internos.
    
- **`0.0.0.0`:** En configuración de servicios, significa "escuchar en todas las interfaces de red disponibles". En tablas de enrutamiento, representa la **Ruta por Defecto** hacia internet.
    
- **IP de Red:** La primera IP de cualquier rango (ej: `192.168.1.0`). Identifica a la red en sí y no se puede asignar a ningún dispositivo.
    
- **IP de Broadcast:** La última IP de cualquier rango (ej: `192.168.1.255`). Se usa para enviar un paquete a _todos_ los dispositivos de la subred a la vez.
    

## 🧮 3. Las Matemáticas del Subnetting (Caso Real)

Cuando "subneteas", le "robas" bits a la parte de host para dárselos a la parte de red. Esto te permite crear subredes más pequeñas y controlar el tamaño de tus dominios de colisión y difusión (broadcast).

### Fórmulas esenciales:

- **Número de subredes creadas:** $2^n$ (donde $n$ es el número de bits robados).

- **Número de hosts útiles por subred:** $2^h - 2$ (donde $h$ es el número de bits que quedan para hosts. Restamos 2 porque la primera IP es la de red y la última es la de broadcast).


### 📝 Ejercicio Práctico del Mundo Real:

Tu empresa tiene la red privada `/24` base: `192.168.1.0`. El departamento de Sistemas necesita separar la red en **2 subredes independientes** (una para Administración y otra para Invitados) para que no se vean entre sí por seguridad.

1. **La red original (`/24`):** Tiene 24 bits de red y 8 de host ($2^8 - 2 = 254$ hosts posibles).
    
2. **Robamos 1 bit:** Pasamos de `/24` a `/25`.
    
3. **Cálculo de subredes:** $2^1 = 2$ subredes creadas. ¡Justo lo que necesitamos!
    
4. **Cálculo de hosts por subred:** Nos quedan 7 bits para hosts. $2^7 - 2 = 128 - 2 = 126$ hosts útiles por subred.
    

La nueva máscara en binario pasa a tener un "1" más en el último octeto (`10000000`), lo que en decimal equivale a **`255.255.255.128`**.

### El resultado final en tu documentación:

|**Subred**|**Rango IP Útil**|**Dirección de Broadcast**|**Máscara (CIDR)**|
|---|---|---|---|
|**Subred 1 (Admin)**|`192.168.1.1` - `192.168.1.126`|`192.168.1.127`|`255.255.255.128` (`/25`)|
|**Subred 2 (Invitados)**|`192.168.1.129` - `192.168.1.254`|`192.168.1.255`|`255.255.255.128` (`/25`)|

> ⚠️ **Nota de Seguridad:** Si un invitado en la `192.168.1.150` intenta atacar o escanear la IP `192.168.1.5` de administración, el paquete se verá forzado a ir al Router (Gateway) porque pertenecen a subredes lógicas diferentes. Si configuras reglas en el router (Firewall/ACLs), podrás bloquear ese tráfico por completo.

_MOC de Referencia:_ [[MOC_Redes_Fundamentos]]