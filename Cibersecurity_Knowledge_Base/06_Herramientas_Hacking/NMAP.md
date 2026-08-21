**Nmap** (siglas de **Network Mapper**, o _Mapeador de Redes_) es la herramienta de código abierto más famosa y utilizada del mundo para el **descubrimiento de redes y la auditoría de seguridad**.

Imagínalo como un radar de barcos, pero para la informática: escupe ráfagas de paquetes de datos hacia una dirección IP (o un rango de ellas) y analiza milimétricamente las respuestas que rebotan para dibujar un mapa exacto de lo que hay al otro lado.

## 🔍 ¿Qué puede hacer Nmap?

Cuando lanzas un escaneo, Nmap es capaz de descifrar cuatro capas de información cruciales sobre el objetivo:

### 1. Descubrimiento de Hosts (¿Quién está vivo?)

Antes de atacar o auditar, necesitas saber qué computadoras están encendidas. Nmap envía peticiones de eco (como un _ping_) para listar qué direcciones IP de una red local o de internet tienen un equipo activo detrás.

### 2. Escaneo de Puertos (¿Qué puertas están abiertas?)

Nmap comprueba el estado de los puertos de una máquina (del puerto 1 al 65535). Tras enviar el paquete, clasifica cada puerto en uno de estos tres estados principales:

- **`open` (Abierto):** Hay un programa escuchando en ese puerto y aceptando conexiones (por ejemplo, una web en el puerto 80). Es una potencial vía de entrada.
    
- **`closed` (Cerrado):** El equipo responde, pero dice que no hay ningún programa corriendo en ese puerto concreto.
    
- **`filtered` (Filtrado):** Nmap no recibe ninguna respuesta. Esto significa que hay un **Firewall** o cortafuegos interceptando el paquete y tirándolo a la basura antes de que llegue al objetivo.
    

### 3. Detección de Versiones y Servicios (¿Qué programa corre ahí?)

No basta con saber que el puerto `80` está abierto. Nmap interroga al puerto (_banner grabbing_) para averiguar el software exacto que lo gestiona y su versión (por ejemplo: `Apache httpd 2.4.41`). Esto es vital porque si esa versión exacta tiene una vulnerabilidad conocida, el auditor puede explotarla.

### 4. Detección de Sistema Operativo (OS Detection)

Analizando sutiles diferencias en la forma en que la pila de red de cada sistema responde a los paquetes defectuosos, Nmap puede adivinar con un porcentaje altísimo de acierto si la máquina remota es **Linux, Windows, macOS** o un dispositivo empotrado (como una impresora o un router).

## Ficha Práctica: Comandos Esenciales de `nmap`

Consolida en tus notas la sintaxis básica y los modificadores clave para auditorías de reconocimiento.

| **Sintaxis Especial**       | **Para qué sirve**                           | **Explicación del truco**                                                                                                                                                             |
| --------------------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nmap 192.168.1.1`          | Escaneo básico inicial.                      | Escanea los **1000 puertos más comunes** de la IP indicada de forma rápida.                                                                                                           |
| `nmap -p 22,80,443`         | Escanear puertos específicos (`-p`).         | Restringe el escaneo únicamente a los puertos indicados, ahorrando muchísimo tiempo y tráfico.                                                                                        |
| `nmap -p-`                  | **Escanear TODO el rango de puertos.**       | Escanea los **65.535 puertos completos**. Obligatorio en auditorías profundas para cazar servicios ocultos o _backdoors_ en puertos raros.                                            |
| `nmap -sV`                  | **Detectar Versiones de Servicios (`-sV`).** | Fuerza a Nmap a interrogar los puertos abiertos para sonsacar el nombre y la versión exacta del software que se ejecuta.                                                              |
| `nmap -O`                   | Detectar Sistema Operativo (`-O`).           | Envía sondas especiales para determinar si el objetivo corre bajo Windows, Linux, etc. _(Requiere privilegios de root/sudo)._                                                         |
| `nmap -A`                   | **El modo "Agresivo" (`-A`).**               | Un combo "todo en uno". Activa la detección de sistema operativo (`-O`), detección de versiones (`-sV`), escaneo de scripts (_NSE_) y traza de ruta (_traceroute_). Hace mucho ruido. |
| `nmap -sS`                  | Escaneo sigiloso (SYN Scan)                  | Envía paquetes SYN pero no llega a completar la conexión TCP (no envía el ACK final). Es más rápido y **evita quedar registrado en muchos _logs_ tradicionales**.                     |
| `nmap -sC`                  | Scripts por defecto                          | Lanza una colección de scripts automáticos de reconocimiento para extraer información extra del objetivo (como títulos de páginas web o usuarios de SSH).                             |
| `nmap -sn`                  | Escaneo de ping                              | No escanea puertos; solo envía un ping a toda la red (`/24`) para **saber qué dispositivos están encendidos** en segundos                                                             |
| `nmap -Pn`                  | Ignora el ping inicial                       | Trata al objetivo como si estuviera encendido obligatoriamente. Evita que Nmap descarte una máquina que tiene un Firewall bloqueando las respuestas de ping (ICMP).                   |
| `nmap -oN reporte.txt [IP]` | Guardar salida en formato normal.            | Guarda el resultado del escaneo en un archivo de texto plano tal y como lo ves en la pantalla para documentar tu auditoría.                                                           |
| `nmap --script vuln [IP]`   | Motor de scripts de Nmap (NSE).              | Lanza la categoría de scripts `vuln` para **buscar vulnerabilidades críticas conocidas** (como _EternalBlue_) de forma automática en el objetivo.                                     |


En **`nmap`**, el flag **`-T5`** (con la T mayúscula) controla la **plantilla de temporizado** (_Timing Template_). Específicamente, `-T5` activa el modo **Insane (Loco / Demencial)**, que es la velocidad máxima absoluta a la que `nmap` puede realizar un escaneo de red.

`nmap` ofrece 6 niveles de velocidad (del `0` al `5`). Al elegir el nivel más alto, estás priorizando la **velocidad extrema** por encima de cualquier otra cosa, sacrificando el sigilo y, potencialmente, la precisión.

## 🏎️ ¿Qué ocurre internamente cuando activas `-T5`?

Para lograr esa velocidad brutal, `-T5` altera de forma estricta los límites internos de tiempos del motor de escaneo:

- **Espera un máximo de 15 minutos por host:** Si un equipo tarda más de eso en responder por completo, `nmap` lo descarta y pasa al siguiente.
    
- **Reduce el tiempo de espera por sonda (_RTT timeout_):** Configura un límite de retransmisión extremadamente bajo (un rango estricto de entre **50 milisegundos y 300 milisegundos**). Si un puerto no responde en ese suspiro de tiempo, se asume que está cerrado o filtrado.
    
- **Reduce los reintentos:** Solo reintenta enviar un paquete **2 veces** antes de darse por vencido con un puerto.
    
- **Sin retraso entre paquetes:** Lanza las sondas de forma simultánea y masiva, saturando el ancho de banda disponible.
    

## El Semáforo de Velocidades en `nmap`

Comparativa estricta de las plantillas de tiempo oficiales de la herramienta.

|**Flag**|**Nombre Oficial**|**Uso Teórico / Escenario de Aplicación**|
|---|---|---|
|`-T0`|Paranoid (Paranoico)|Ultra lento. Espera 5 minutos entre cada sonda. Diseñado de forma estricta para evadir Sistemas de Detección de Intrusos (IDS) clásicos.|
|`-T1`|Sneaky (Furtivo)|Espera 15 segundos entre paquetes. Uso en auditorías muy intrusivas donde se quiere mitigar el riesgo de bloqueo de IPs.|
|`-T2`|Polite (Cortés)|Consume menos ancho de banda. Reduce la velocidad para no tirar servicios frágiles o saturar servidores antiguos.|
|`-T3`|**Normal**|**Es el modo por defecto.** Si no pones ningún flag de velocidad, `nmap` usa este. Equilibrio perfecto entre velocidad y fiabilidad en redes modernas.|
|`-T4`|Aggressive (Agresivo)|**El recomendado para CTFs y Auditorías.** Acelera el escaneo asumiendo que estás en una red moderna, estable y con buen ancho de banda.|
|`-T5`|**Insane (Loco)**|**Velocidad máxima.** Diseñado únicamente para redes locales cableadas (LAN) ultra rápidas o cuando el tiempo apremia y no te importa hacer ruido.|

### 💡 Los 3 peligros mortales de usar `-T5` en el mundo real

Aunque es tentador usar siempre la velocidad máxima para terminar rápido, en entornos reales o de producción corporativa, usar `-T5` suele acabar mal por tres motivos:

1. **Falsos Negativos (Falta de precisión):** Al dar tan poco margen de tiempo para que los puertos respondan (máximo 300ms), si la red sufre una pequeña fluctuación o congestión, los paquetes de respuesta legítimos llegarán tarde. `nmap` los dará por perdidos y te reportará puertos como **CERRADOS** cuando en realidad estaban **ABIERTOS**.
    
2. **Es un imán de alertas (Cero sigilo):** El modo `-T5` hace tanto ruido en la red que saltará absolutamente todas las alarmas de cualquier Firewall, IDS o IPS corporativo en los primeros 3 segundos, lo que provocará el baneo automático de tu dirección IP.
    
3. **Denegación de Servicio (DoS):** Al escupir miles de paquetes por segundo, si apuntas a un dispositivo de red antiguo, un router doméstico o un dispositivo IoT (cámaras, impresoras), puedes llegar a **congelar o tirar el dispositivo** por saturación de su pila de red.
    

> 📌 **El consejo del analista:** En el 90% de tus auditorías y laboratorios, usa **`-T4`**. Es sumamente rápido, pero mantiene la suficiente tolerancia de tiempo para no perder puertos abiertos por el camino. Deja el `-T5` exclusivamente para redes locales directas (`127.0.0.1` o redes locales cableadas de altas prestaciones).