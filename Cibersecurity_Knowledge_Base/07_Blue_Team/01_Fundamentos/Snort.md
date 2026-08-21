---

tags: [ciberseguridad, ids, ips, snort, cisco, blue_team, reglas, red_team]
---

# Snort

**Snort** es un sistema de prevención y detección de intrusiones en red (NIDS/NIPS) de código abierto creado originalmente por Martin Roesch en 1998 y actualmente mantenido por **Cisco Systems**. Es el estándar de facto de la industria para el análisis de tráfico en tiempo real y el registro de paquetes (*packet logging*) en redes IP.

Snort utiliza un motor de inspección basado en **reglas de texto plano** que permite evaluar el contenido de las cabeceras de red, los puertos de origen/destino y la carga útil (*payload*) de los paquetes para identificar actividades maliciosas, escaneos de puertos y exploits conocidos.

## ⚙️ 1. Modos de Ejecución de Snort

Snort puede ejecutarse en tres modos operativos principales según la sintaxis pasada por línea de comandos:

### 1. Modo Sniffer

Lee los paquetes de la red y los muestra por consola en tiempo real (similar a `tcpdump`).

```bash
snort -v -i eth0
```

* `-v`: Muestra los encabezados de las capas TCP/IP.
* `-i eth0`: Especifica la interfaz de red a monitorizar.

### 2. Modo Packet Logger

Registra los paquetes capturados en el disco duro para su posterior análisis forense (en formato binario `pcap` o texto).

```bash
snort -dev -l /var/log/snort -b
```

* `-d`: Incluye el payload de la capa de aplicación.
* `-e`: Muestra los encabezados de la capa de enlace de datos (MAC).
* `-l`: Directorio de destino para los registros de log.
* `-b`: Guarda los logs en formato binario `pcap` para mayor rendimiento.

### 3. Modo NIDS / NIPS (Análisis con Reglas)

Analiza el tráfico aplicando un conjunto de reglas de seguridad cargadas desde un archivo de configuración (`snort.conf`).

```bash
snort -A console -q -c /etc/snort/snort.conf -i eth0
```

* `-A console`: Envía las alertas directamente a la pantalla de la consola.
* `-q`: Modo silencioso (*Quiet*), no muestra el banner inicial ni estadísticas al cerrar.
* `-c`: Ruta al archivo de configuración de reglas.

---

## 📝 2. Estructura y Sintaxis de las Reglas de Snort

Las reglas de Snort se dividen en dos partes fundamentales: el **Encabezado de la Regla** (*Rule Header*) y las **Opciones de la Regla** (*Rule Options*).

```text
[Acción] [Protocolo] [IP_Origen] [Puerto_Origen] -> [IP_Destino] [Puerto_Destino] ([Opciones_de_Regla])
```

### Ejemplo Práctico: Regla para Detección de Reverse Shell por Netcat

```text
alert tcp any any -> 192.168.1.0/24 any (msg:"POSIBLE REVERSE SHELL NETCAT"; content:"/bin/bash"; sid:1000001; rev:1;)
```

### Desglose de Parámetros:

* **Acción (`alert`):** Qué hace Snort cuando la regla coincide. Opciones comunes: `alert` (genera alerta y loguea), `log` (solo registra), `drop` (bloquea el paquete en modo NIPS/Inline).
* **Protocolo (`tcp`):** Protocolo de transporte a inspeccionar (`tcp`, `udp`, `icmp`, `ip`).
* **Origen y Destino (`any any -> 192.168.1.0/24 any`):** Define las direcciones IP y puertos de origen/destino. Se pueden usar variables de entorno fijadas en `snort.conf` como `$HOME_NET` o `$EXTERNAL_NET`.
* **`msg:"..."`:** El texto descriptivo que aparecerá en el evento del SIEM o consola cuando salte la alerta.
* **`content:"/bin/bash"`:** Busca la cadena exacta de bytes dentro del payload del paquete. Si la cadena coincide, la regla se dispara.
* **`sid:1000001`:** *Snort ID*. Identificador único de la regla. Los SIDs menores a 1000000 están reservados para reglas oficiales de Cisco; los números superiores a 1000000 se usan para reglas personalizadas (*custom*).
* **`rev:1`:** Número de revisión de la regla.

---

## 🥷 3. Auditoría y Evasión de Reglas de Snort

Para evaluar la efectividad de un despliegue de Snort durante un ejercicio de Red Team, los auditores prueban los límites del motor de inspección:

### 1. Evasión por Formatos Hexadecimales / URL Encoding

Si una regla busca una cadena estática en texto plano (ejemplo: `content:"SELECT * FROM"`), un atacante puede codificar la carga útil en formato Hexadecimal o URL Encoding (`%53%45%4C%45%43%54`).

* **Defensa en Snort:** Las reglas avanzadas deben incluir modificadores de contenido como `http_uri` o la opción `nocase` para normalizar el tráfico web antes de buscar la firma.

### 2. Saturación del Motor (Agotamiento de CPU)

Snort 2 (monohilo) puede sufrir caídas o pérdida de paquetes si se le envían patrones que obligan al motor de expresiones regulares (*PCRE*) a realizar búsquedas complejas con un algoritmo ineficiente (*Backtracking*).

* **Defensa en Snort:** Utilizar Snort 3 (arquitectura multihilo) e implementar la opción `fast_pattern` en las reglas para realizar un filtrado previo rápido antes de procesar reglas costosas.

*MOC de Referencia:* [[MOC_IDS_IPS]]