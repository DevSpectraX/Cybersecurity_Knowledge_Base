---
tags: [redes, networking, wireshark, tshark, pcap, sniffing]
---

**Wireshark** es el analizador de protocolos de red (Sniffer) más importante y utilizado del mundo. Permite capturar e interactuar con el tráfico que pasa por una interfaz de red en tiempo real, desglosando los paquetes bit a bit según las capas de los modelos OSI y TCP/IP.

**Tshark** es su equivalente nativo para la línea de comandos, una herramienta imprescindible cuando se auditan servidores sin entorno gráfico (como la mayoría de servidores Linux en producción).

En ciberseguridad, estas herramientas son el pilar fundamental para el análisis forense digital, la respuesta a incidentes (detección de malware o exfiltración) y el desarrollo de exploits.

## 🧲 1. Conceptos Clave del Sniffing de Red

Antes de abrir Wireshark, el sistema operativo y la tarjeta de red deben prepararse para interceptar el tráfico:

- **Modo Promiscuo (Promiscuous Mode):** Por defecto, una tarjeta de red (NIC) descarta cualquier trama cuya dirección MAC de destino no sea la suya. Al activar el modo promiscuo, la tarjeta acepta **todas** las tramas que pasen por el cable o el aire, permitiendo a Wireshark capturar el tráfico ajeno dentro de un mismo dominio de colisión.
    
- **Archivos `.pcap` y `.pcapng`:** Son los formatos estándar de la industria para guardar capturas de red. Un analista de seguridad suele recibir un archivo `.pcap` extraído de un sensor de red (como un IDS o un Firewall) para investigar un incidente de seguridad de forma offline.
    

## 🔍 2. Filtros de Captura vs Filtros de Visualización

Capturar todo el tráfico de una red empresarial satura rápidamente la memoria RAM y el disco duro. Por eso, Wireshark divide sus filtros en dos filosofías totalmente distintas:

### A) Filtros de Captura (Capture Filters)

Se configuran **antes** de empezar a grabar el tráfico. Utilizan la sintaxis _BPF (Berkeley Packet Filter)_. Si un paquete no cumple la regla, la tarjeta de red lo descarta de inmediato y no se almacena.

- `tcp port 80`: Captura únicamente tráfico web HTTP.
    
- `host 192.168.1.100`: Captura solo paquetes donde esa IP sea el origen o el destino.
    
- `not port 22`: Captura todo excepto las conexiones SSH (para evitar capturar tu propia sesión de administración).
    

### B) Filtros de Visualización (Display Filters)

Se aplican **después** de haber realizado la captura. Todo el tráfico está guardado, pero solo se te muestra en pantalla lo que buscas. Su sintaxis es más potente y específica:

- `http.request.method == "POST"`: Muestra solo peticiones web que envíen datos (formularios, inicios de sesión).
    
- `ip.addr == 10.0.0.5 and tcp.flags.syn == 1`: Busca intentos de inicio de conexión TCP (`SYN`) que involucren a esa IP específica.
    
- `dns.flags.response == 0`: Muestra únicamente las preguntas DNS de los clientes, ocultando las respuestas de los servidores.
    

## 💻 3. Tshark: El Potencial de la Línea de Comandos

Tshark incluye todo el motor de análisis de Wireshark pero empaquetado para la terminal de comandos. Es ideal para automatizar tareas con scripts y procesar archivos `.pcap` masivos de forma ultra rápida.

### 🛠️ Comandos Esenciales de Tshark en Auditorías:

#### 1. Listar las interfaces de red disponibles para capturar:

```bash
tshark -D
```

#### 2. Capturar tráfico en tiempo real en una interfaz guardándolo en un archivo:

```bash
tshark -i eth0 -w captura_incidente.pcap
```

#### 3. Leer un archivo `.pcap` aplicando un filtro de visualización (Ej: buscar peticiones HTTP GET):

```bash
tshark -r captura_incidente.pcap -Y 'http.request.method == "GET"'
```

#### 4. Extraer campos específicos en formato limpio (ideal para análisis de datos):

Si quieres extraer de un vistazo todas las IPs de origen que están haciendo consultas DNS en una captura:

```bash
tshark -r captura_incidente.pcap -Y "dns" -T fields -e ip.src -e dns.qry.name
```

## 💀 Enfoque de Ciberseguridad: Técnicas de Análisis Forense

Frente a una captura de red sospechosa, un analista de seguridad busca patrones anómalos o fugas de información utilizando tres funciones estrella de Wireshark:

### 1. Seguir el Flujo (Follow TCP/UDP Stream)

Los paquetes viajan fragmentados en la red. Wireshark permite hacer clic derecho sobre cualquier paquete y seleccionar `Follow -> TCP Stream`. Esta función junta todos los segmentos dispersos y los reconstruye en una ventana de texto continuo cronológica, mostrando la conversación exacta tal y como la vieron las aplicaciones. Si el tráfico es en texto plano (como HTTP o Telnet), **verás las contraseñas y comandos introducidos directamente con tus ojos**.

### 2. Exportar Objetos (Export Objects)

Si un usuario ha descargado un archivo malicioso por HTTP o un atacante ha exfiltrado un PDF confidencial por FTP, Wireshark puede reconstruir ese archivo a partir de los bytes capturados en la red. Yendo a `File -> Export Objects -> HTTP`, puedes guardar el binario (`.exe`, `.pdf`, `.zip`) directamente en tu disco duro para analizarlo en un entorno seguro o mandarlo a VirusTotal.

### 3. Credenciales en Texto Plano

Buscar credenciales en capturas de protocolos inseguros es trivial usando filtros de visualización enfocados en palabras clave:

- Para FTP (Puerto 21): El filtro `ftp.request.command == "USER" or ftp.request.command == "PASS"` aislará instantáneamente el usuario y la contraseña del objetivo.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Redes]]