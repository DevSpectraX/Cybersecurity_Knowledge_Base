---
tags: [ciberseguridad, ids, ips, blue_team]
---
# 🛡️ MOC: Sistemas de Detección y Prevención (IDS / IPS)

Este mapa conceptual centraliza las tecnologías de monitorización defensiva, inspección de paquetes en tiempo real, protección de endpoints y las técnicas de evasión utilizadas para auditar la efectividad de los controles de red.

## 🚨 1. Fundamentos y Arquitectura de IDS / IPS
Los mecanismos principales de análisis de tráfico, diferencias operativas entre detección y prevención, y modelos de despliegue.

- [[IDS vs IPS]]: Diferencias clave entre la inspección pasiva por copia de tráfico (Detección / Alertas) y la inspección activa en línea (Prevención / Bloqueo).
- [[NIDS y NIPS]]: Sistemas de protección basados en red para analizar el tráfico bruto en segmentos completos (`Snort`, `Suricata`, `Zeek`).
- [[HIDS y HIPS]]: Sistemas de protección basados en host para monitorizar eventos del SO, integridad de archivos y llamadas al sistema (`Wazuh`, `OSSEC`).

## 🛠️ 2. Motores de Inspección y Herramientas Defensivas
Plataformas y herramientas estándar de la industria para el análisis de firmas, detección de anomalías y gestión de eventos.

- [[Snort]]: Motor de detección de intrusiones de red estándar, análisis de firmas en texto plano y creación de reglas personalizadas.
- [[Suricata]]: IDS/IPS multihilo de alto rendimiento para inspección profunda de paquetes (DPI) y extracción de metadatos de red.
- [[Wazuh]]: Plataforma de ciberseguridad Open Source para análisis de datos de telemetría, HIDS, monitorización de integridad (FIM) y SIEM.

## 🥷 3. Vectores de Evasión y Auditoría (Red Team)
Técnicas tácticas para evaluar la eficacia de las reglas de detección y sobrepasar la inspección de paquetes.

- [[Evasión con Nmap]]: Técnicas de fragmentación de paquetes (`-f`), simulación de origen mediante señuelos (`-D`) y manipulación de tiempos (`-T`).
- [[Ofuscación de Tráfico]]: Encapsulamiento y cifrado de conexiones (*Reverse Shells*) mediante TLS/SSL para evadir la inspección profunda de paquetes (DPI).

MOCs Relacionados: 
[[MOC_Fundamentos_Redes]] 
[[MOC_Conceptos_Seguridad]]
[[MOC_Administracion_Linux]]