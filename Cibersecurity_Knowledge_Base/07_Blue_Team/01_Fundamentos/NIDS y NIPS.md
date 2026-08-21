---
tags: [ciberseguridad, ids, ips, nids, nips, snort, suricata, zeek, blue_team, redes]
---

# NIDS y NIPS (Network Intrusion Detection / Prevention Systems)

Un **NIDS** (Sistema de Detección de Intrusiones en Red) y un **NIPS** (Sistema de Prevención de Intrusiones en Red) son soluciones de seguridad orientadas a la inspección centralizada del tráfico que circula por los diferentes segmentos de una red local (LAN), zona desmilitarizada (DMZ) o enlaces perimetrales.

A diferencia de los sistemas basados en host (HIDS/HIPS), un NIDS/NIPS opera de manera independiente al sistema operativo de los equipos finales: analiza las cabeceras e inspecciona la carga útil (*payload*) de los paquetes de red a nivel de las capas 3 (Red), 4 (Transporte) y 7 (Aplicación) del modelo OSI.

## 🏗️ 1. Arquitectura de Despliegue y Captura de Tráfico

Para que un NIDS o NIPS pueda analizar la información, debe implementarse mediante mecanismos específicos de captura de interfaz en la infraestructura de red:

### 1. Despliegue de NIDS (Captura Pasiva)

El NIDS utiliza una interfaz en modo promiscuo conectada a puntos de agregación de tráfico:

* **Puerto SPAN / Mirror (Switch Port Analyzer):** Se configura un puerto en el switch corporativo para duplicar todo el tráfico de una o varias VLANs y reenviarlo hacia el puerto donde escucha la interfaz del NIDS.
* **TAP de Red (Test Access Point):** Dispositivo de hardware pasivo intercalado en el cableado estructurado que divide la señal física (de cobre o fibra óptica) para entregar una copia exacta del flujo sin introducir latencia ni depender del software del switch.

### 2. Despliegue de NIPS (Captura Activa Inline)

El NIPS requiere al menos dos interfaces físicas configuradas en modo **Bridge (Puente Transparente)** sin dirección IP asignada en el plano de datos, situándose en el camino del tráfico entre el router/firewall y el switch interno.

> 🛠️ **Mecanismo de Salvaguarda (Bypass Hardware):** Los dispositivos NIPS dedicados integran tarjetas de red con relés electromecánicos (*Bypass NICs*). Si el dispositivo pierde la alimentación eléctrica o el proceso del software se cuelga, el relé físico une internamente el par de cobre para mantener la continuidad del enlace a costa de dejar pasar el tráfico sin inspección.

---

## 🛠️ 2. Motores Estándar de la Industria

Existen tres arquitecturas principales utilizadas en entornos de producción y SOC (Security Operations Center):

### 1. Snort (Cisco)

Motor clásico de detección basado en reglas escritas en texto plano. En sus versiones tradicionales (Snort 2) utiliza un procesamiento monohilo, mientras que Snort 3 soporta multihilo nativo y análisis sintáctico mejorado de protocolos.

* **Uso principal:** Inspección rápida basada en firmas de contenido estático y coincidencias de patrones mediante expresiones regulares.

### 2. Suricata (OISF)

Motor Open Source multihilo desde su concepción inicial, capaz de escalar en procesadores multinúcleo para inspeccionar enlaces de 10 Gbps o superiores.

* **Diferenciador clave:** Integra extracción automática de metadatos de capa de aplicación (registros HTTP, TLS/SSL, DNS, SSH), inspección profunda de archivos (*File Extraction*) y soporte nativo para reglas con sintaxis compatible con Snort.

### 3. Zeek (Anteriormente Bro)

No opera como un motor clásico de coincidencia de firmas, sino como un analizador de comportamiento de protocolos y motor de telemetría de red.

* **Diferenciador clave:** Traduce el tráfico bruto de red en eventos estructurados y genera *logs* detallados por protocolo (archivos `conn.log`, `dns.log`, `http.log`, `ssl.log`) mediante un lenguaje de scripting propio orientado a la detección de anomalías complejas.

---

## 🥷 3. Vectores de Auditoría y Limitaciones Técnicas

Al auditar o evaluar un NIDS/NIPS, se deben considerar sus fronteras de efectividad técnica:

### 1. Impacto del Tráfico Cifrado (TLS 1.3 / HTTPS)

Si el tráfico entre el cliente y el servidor utiliza cifrado TLS, la carga útil del paquete de capa 7 resulta ilegible para un NIDS/NIPS estándar.

* **Solución técnica:** Implementar dispositivos de **TLS Decryption / SSL Inspection** (Proxy inverso o firewall de inspección profunda) que descifren el tráfico antes de entregarlo al motor NIDS/NIPS, o depender del análisis de metadatos como la extensión **SNI (Server Name Indication)** y los huellas digitales de certificados **JA3 / JA4**.

### 2. Agotamiento de Memoria por Asimetría de Rutas

Si la red cuenta con enrutamiento asimétrico (el tráfico de ida pasa por un camino y el de vuelta por otro), el NIDS/NIPS no puede reconstruir el estado de la sesión TCP (*TCP Stream Reassembly*), lo que invalida las reglas de detección que requieren analizar el contexto completo de la conexión.

*MOC de Referencia:* [[MOC_IDS_IPS]]