---
tags: [ciberseguridad, red_team, nmap, evasion, ids, ips, scanning, blue_team]
---
# Evasión con Nmap

Durante la fase de reconocimiento de una auditoría de seguridad o ejercicio de *Red Team*, la ejecución de escaneos de puertos estándar suele ser detectada de inmediato por los sistemas NIDS/NIPS (*Snort*, *Suricata*) debido a los patrones de tráfico volumétricos y la firma estática de las cabeceras de **Nmap**.

Para evaluar la capacidad de respuesta y los límites de los controles de red defensivos, Nmap incluye parámetros avanzados diseñados para alterar la estructura de los paquetes IP/TCP, fragmentar la carga útil y manipular el temporizado del escaneo.

## 🛠️ 1. Técnicas Principales de Evasión en Nmap

| Sintaxis / Flag | Mecanismo Técnico | Efecto en el NIDS/NIPS |
| --- | --- | --- |
| **`-f`** / **`--mtu <bytes>`** | Fragmenta las cabeceras IP del paquete en trozos de 8 bytes (o el valor asignado en MTU, múltiplo de 8). | Divide la cabecera TCP en múltiples fragmentos IP. Si el NIDS no reensambla los paquetes en memoria antes de aplicar las reglas, la firma no coincide y el escaneo pasa desapercibido. |
| **`-D RND:10`** / **`-D ME,decoy1,decoy2`** | Genera señuelos (*Decoys*). Envía copias del escaneo falsificando la IP de origen con direcciones aleatorias o específicas. | El NIDS detecta el escaneo pero registra múltiples direcciones IP atacantes simultáneas, imposibilitando identificar la IP real del auditor sin un análisis de correlación avanzado. |
| **`-g <puerto>`** / **`--source-port`** | Fuerza a Nmap a enviar todos los paquetes de prueba desde un puerto de origen específico (ej. `-g 53` o `-g 80`). | Explotación de reglas de firewall/NIDS permisivas que confían a ciegas en el tráfico proveniente de puertos del sistema como DNS (53) o HTTP/HTTPS (80/443). |
| **`--data-length <bytes>`** | Añade bytes de relleno (*padding*) aleatorios a los paquetes TCP/UDP enviados. | Rompe las firmas de NIDS basadas en el tamaño exacto del paquete o en la ausencia de carga útil (*payload*) en paquetes SYN/UDP. |
| **`-T0`** / **`-T1`** | Modifica la agresividad del temporizador. `-T0` (*Paranoid*) espera 5 minutos entre cada paquete; `-T1` (*Sneaky*) espera 15 segundos. | Evade la detección por umbral de frecuencia (*rate-limiting*) o detección de anomalías volumétricas en el NIDS/NIPS. |

---

## 💻 2. Ejemplos Prácticos de Comandos de Evasión

### 1. Escaneo Fragmentado con Inyección de Bytes Aleatorios

Escaneo SYN sigiloso (`-sS`), deshabilitando la resolución DNS (`-n`) y el ping previo (`-Pn`), aplicando fragmentación y tamaño de payload personalizado:

```bash
nmap -sS -Pn -n -f --data-length 24 -p 22,80,443 192.168.1.50

```

### 2. Escaneo con Señuelos (*Decoys*) y Puerto de Origen Falsificado

Oculta la dirección IP real entre 5 direcciones de origen aleatorias (`RND:5`) e indicando la dirección del propio atacante (`ME`), enviando la petición desde el puerto UDP 53 (DNS):

```bash
nmap -sS -Pn -D RND:5,ME -g 53 -p 21,22,80 192.168.1.50

```

### 3. Modificación de la Banderas TCP (Crafting de Paquetes)

Envía paquetes con banderas TCP no estándar para evadir reglas de detección basadas en el saludo de tres vías (*3-Way Handshake*):

```bash
nmap -sF -Pn 192.168.1.50      # FIN Scan: Envía solo la bandera FIN
nmap -sX -Pn 192.168.1.50      # Xmas Scan: Activa las banderas FIN, PSH y URG
nmap -sN -Pn 192.168.1.50      # Null Scan: Envía el paquete sin ninguna bandera TCP activa
```

---

## 🛡️ 3. Perspectiva Defensiva (Blue Team Countermeasures)

Los administradores de seguridad y analistas del SOC implementan las siguientes medidas para mitigar la evasión mediante Nmap:

1. **Reensamblado IP Obligatorio (*IP Defragmentation*):** Configurar el cortafuegos perimetral o NIPS para reensamblar todos los fragmentos de paquetes IP en memoria antes de pasarlos por el motor de reglas de inspección profunda (DPI).
2. **Definición Estricta de Estado (*Stateful Inspection*):** Descartar cualquier paquete TCP entrante con combinaciones de banderas anómalas (como las utilizadas en Null, Xmas o FIN Scan) que no pertenezcan a una sesión establecida previamente.
3. **Análisis de Anomalías Temporal:** Configurar alertas de SIEM para detectar patrones de escaneo distribuidos (*Slow Scans*) que utilicen tiempos elevados entre peticiones desde la misma subred.

*MOC de Referencia:* [[MOC_IDS_IPS]]