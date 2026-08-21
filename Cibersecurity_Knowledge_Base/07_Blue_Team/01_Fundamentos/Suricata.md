---
tags: [ciberseguridad, ids, ips, suricata, oisf, blue_team, dpi, reglas]
---
# Suricata

**Suricata** es un motor de detección de intrusiones en red (NIDS), prevención de intrusiones (NIPS) y monitorización de seguridad de red (NSM) de código abierto mantenido por la **OISF** (*Open Information Security Foundation*). Se ha establecido como la alternativa moderna y de alto rendimiento frente a motores tradicionales gracias a su arquitectura nativa multihilo (*multi-threading*).

Suricata es capaz de realizar inspección profunda de paquetes (DPI) en enlaces de alta velocidad (10 Gbps, 40 Gbps o superiores), extraer archivos adjuntos del tráfico al vuelo y generar telemetría detallada en formato JSON compatible de forma nativa con arquitecturas SIEM (como el Elastic Stack o Wazuh).

## ⚡ 1. Arquitectura Multihilo y Motor de Inspección

A diferencia de Snort 2 (que opera en un único hilo por proceso), Suricata distribuye la carga del procesamiento de paquetes entre todos los núcleos de CPU disponibles en el sistema.

### Modelos de Procesamiento de Hilos:

* **Modo Auto (AutoRun):** Suricata crea automáticamente hilos de procesamiento según el número de núcleos detectados. Cada hilo ejecuta de forma independiente la captura, decodificación, reensamblado de flujos y evaluación de reglas para un conjunto de paquetes.
* **Procesamiento de Flujos (*Flow Engine*):** Mantiene el estado de las sesiones TCP/UDP completas en memoria, permitiendo analizar transacciones completas de capa de aplicación sin perder el contexto aunque los paquetes lleguen desordenados.

> 🛠️ **Formato de Salida Unificado (`eve.json`):** Toda la actividad de Suricata (alertas, eventos de red, metadatos HTTP/DNS/TLS y extracción de archivos) se escribe en un único archivo de registro estructurado llamado `eve.json`. Esto elimina la necesidad de usar procesadores de registros adicionales (*log parsers*) complejos.

---

## 📝 2. Sintaxis de Reglas y Capacidades Avanzadas

Suricata es compatible con el formato de reglas de Snort, pero añade palabras clave avanzadas para la inspección de protocolos modernos de capa de aplicación.

```text
[Acción] [Protocolo] [Origen] [Puerto_Origen] -> [Destino] [Puerto_Destino] ([Opciones_de_Regla])
```

### Ejemplo Práctico: Regla con Extracción y Análisis de Certificados TLS

```text
alert tls $EXTERNAL_NET any -> $HOME_NET any (msg:"ALERTA: Certificado TLS Sospechoso Deteccion Cobalt Strike"; tls.cert_subject; content:"CN=Major League Baseball"; sid:2000001; rev:1;)
```

### Desglose de Opciones Extendidas de Suricata:

* **`tls.cert_subject`:** Le indica al motor que inspeccione específicamente el campo *Subject* del certificado TLS, incluso si el cuerpo de la comunicación posterior va a ser cifrado.
* **`app-layer-protocol`:** Permite identificar protocolos en puertos no estándar (por ejemplo, detectar tráfico HTTP ejecutándose sobre el puerto 4444) mediante inspección de firmas de capa 7, ignorando el puerto de transporte.
* **`filestore`:** Opción de regla para extraer automáticamente a disco cualquier archivo transferido por la red (vía HTTP, SMB o FTP) que haga *match* con una firma para su posterior análisis en un *Sandbox*.

---

## 🥷 3. Perspectiva de Red Team: Desafíos y Evasión en Suricata

Al auditar una infraestructura protegida por Suricata, los equipos ofensivos deben tener en cuenta sus capacidades de análisis de estado:

### 1. Detección por Huella Digital TLS (JA3 / JA4)

Suricata genera de forma nativa huellas digitales **JA3** y **JA3S** para las conexiones TLS entrantes y salientes basadas en los parámetros de la negociación (*Client Hello*).

* **Efecto:** Aunque el payload de la *Reverse Shell* esté cifrado con SSL/TLS, si la herramienta del atacante (ej. *Empire*, *Cobalt Strike* o un cliente `python` por defecto) utiliza una pila TLS conocida, Suricata la identifica y bloquea por la firma de su cliente TLS, no por el contenido.
* **Técnica de Evasión:** Modificar las bibliotecas de cifrado o usar *malleable C2 profiles* para suplantar la huella JA3 de un navegador legítimo (como Google Chrome o Firefox).

### 2. Evitar la Inspección Multihilo mediante Dispersión de Flujos

Suricata agrupa el tráfico por flujos (IP origen, IP destino, Puerto origen, Puerto destino, Protocolo). Si todo el ataque se canaliza por una sola sesión TCP, un único hilo de CPU procesará toda la carga.

* **Técnica de Auditoría:** Diversificar el tráfico entre múltiples direcciones de origen (usando proxys o redes de botnets) para forzar al motor a repartir la carga entre hilos, buscando puntos de saturación si los recursos de memoria del servidor del NIDS/NIPS son limitados.

*MOC de Referencia:* [[MOC_IDS_IPS]]