---
tags:
  - redes
  - networking
  - cli
  - comandos
  - diagnostico
  - enumeracion
---
En el ámbito de la ciberseguridad, la administración de sistemas y la respuesta a incidentes, las herramientas de línea de comandos (**CLI**) son el primer recurso para verificar la conectividad, mapear topologías de red y descubrir conexiones sospechosas. Depender de interfaces gráficas no es viable cuando se auditan servidores remotos o se analiza una máquina comprometida a través de una Shell inversa.

A continuación se detallan los comandos nativos fundamentales en entornos Linux y Windows con casos de uso reales orientados a la auditoría de infraestructura.

## 📡 1. PING (Packet Internet Groper)

Utiliza el protocolo **ICMP** (Capa de Internet) enviando paquetes _Echo Request_ (Tipo 8) y esperando respuestas _Echo Reply_ (Tipo 0). Su uso principal es comprobar si un host remoto está encendido y medir la latencia de la conexión.

- **Comando básico (ej: comprobar conectividad con un servidor DNS público):**
```
ping -c 4 8.8.8.8
```
  
   _(El parámetro `-c 4` en Linux limita los envíos a 4 paquetes, ya que por defecto es infinito. En Windows, por defecto siempre envía 4)._


### 💀 Enfoque de Ciberseguridad: Análisis del TTL (Time to Live)

El campo **TTL** de la respuesta ICMP indica cuántos routers (saltos) puede atravesar el paquete antes de ser descartado. Cada sistema operativo tiene un valor TTL inicial por defecto, lo que permite realizar un **reconocimiento pasivo del sistema operativo del objetivo** con un simple ping:

|**Sistema Operativo**|**TTL Inicial Estimado**|
|---|---|
|**Linux / Unix**|**64**|
|**Windows**|**128**|
|**Dispositivos de Red (Cisco, Routers...)**|**255**|

- **Caso Real:** Si lanzas un ping a un servidor de tu red local y recibes un `ttl=64`, sabes de inmediato que estás ante una máquina Linux. Si el servidor está en internet y recibes un `ttl=52`, significa que la máquina origen probablemente era un Linux (`64`) y el paquete ha atravesado 12 routers por el camino ($64 - 12 = 52$).


## 🗺️ 2. TRACEROUTE / TRACERT

Muestra la ruta exacta (los routers intermedios) que atraviesa un paquete desde tu máquina hasta el host de destino. Es fundamental para mapear la topología de una red y detectar dónde se corta una comunicación.

- **En Linux:** `traceroute google.com`

- **En Windows:** `tracert google.com`


### ⚙️ El truco técnico detrás de Traceroute:

Traceroute no utiliza un protocolo mágico de enrutamiento; **manipula intencionadamente el campo TTL de los paquetes**:

1. Envía un primer paquete con **`TTL = 1`**. El primer router de la red lo recibe, resta 1 al TTL, ve que llega a 0 y descarta el paquete, enviando de vuelta un mensaje de error ICMP: _"Time Exceeded (Tiempo excedido en tránsito)"_. Tu máquina anota la IP de ese router (Salto 1).
    
2. Envía un segundo paquete con **`TTL = 2`**. Pasa el primer router y muere en el segundo. Tu máquina anota la IP del segundo router (Salto 2).
    
3. Incrementa el TTL sucesivamente hasta que el paquete llega al destino final, dibujando el mapa de nodos completo.
    

> ⚠️ **Nota de Seguridad:** En auditorías externas, es muy común ver que a partir de cierto salto, `traceroute` solo muestra asteriscos (`* * *`). Esto no significa que internet se haya roto; significa que has topado con un **Firewall** o un balanceador de carga configurado para descartar paquetes ICMP de diagnóstico por seguridad.

## 👁️ 3. NETSTAT (Network Statistics) y SS

Es la herramienta de auditoría interna por excelencia. Muestra todas las conexiones de red activas (entrantes y salientes), las tablas de enrutamiento y, lo más importante, **qué proceso interno del sistema operativo está abriendo cada puerto**.

> 💡 **Consejo Pro (Linux):** El comando clásico `netstat` está considerado obsoleto en algunas distribuciones modernas, siendo reemplazado por **`ss`** (Socket Statistics), que es mucho más rápido al consultar directamente el kernel. Sin embargo, los parámetros principales son idénticos.

### 🔍 Comandos de Enumeración Críticos en Ciberseguridad:

#### A) Listar todos los puertos en escucha y conexiones activas con sus números de puerto:

- **En Linux:** `ss -tunlp` o `netstat -tunlp`
    
- **En Windows:** `netstat -ano`
    

#### Significado de los modificadores estándar (Linux):

- `-t`: Muestra sockets **T**CP.
    
- `-u`: Muestra sockets **U**DP.
    
- `-n`: Muestra direcciones y puertos en formato **N**umérico (evita que el DNS intente traducir las IPs, acelerando la respuesta).
    
- `-l`: Muestra únicamente los puertos que están en **L**istening (escuchando conexiones entrantes).
    
- `-p`: Muestra el **P**rograma o proceso (PID) dueño de esa conexión (requiere privilegios de _root_ o _Administrador_).
    

### 🚨 Caso Práctico en Respuesta a Incidentes (Caza de Amenazas)

Estás analizando un servidor Windows comprometido y sospechas que un malware ha abierto una conexión hacia el exterior para recibir órdenes (un canal de Comando y Control - C2).

Ejecutas en la consola `cmd`:

DOS

```
netstat -ano
```

Y localizas la siguiente línea sospechosa:

Plaintext

```
  Proto  Dirección local        Dirección remota       Estado          PID
  TCP    192.168.1.50:49215     45.33.22.11:4444       ESTABLISHED     3412
```

#### 🕵️‍♂️ Análisis forense rápido:

1. **`ESTABLISHED`:** Hay una conexión de red activa en este preciso instante.
    
2. **`45.33.22.11:4444`:** Tu servidor interno se ha conectado a una IP externa desconocida a través de un puerto muy asociado a herramientas de explotación como Metasploit (`4444`).
    
3. **`PID 3412`:** El identificador del proceso responsable es el 3412.
    

Para fulminar la amenaza y averiguar qué binario es, cruzas el dato inmediatamente con la gestión de procesos del sistema:

- **En Windows:** `tasklist /FI "PID eq 3412"` (Te dirá el nombre del `.exe` malicioso detrás del puerto).
    
- **En Linux (si fuera el caso):** `ps -p 3412 -f` o `ls -l /proc/3412/exe`.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Redes]]