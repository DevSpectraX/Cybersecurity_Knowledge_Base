---
tags: [redes, networking, fundamentos, moc, indice]
---

Este mapa conceptual centraliza toda la teoría de conectividad, arquitectura de red, protocolos de comunicación y análisis de tráfico real. Sirve como base para comprender cómo interactúan los sistemas operativos y cómo se transmiten los datos a través de infraestructuras locales y globales.



---

## 🧭 1. Modelos de Referencia y Arquitectura
* [[01_Modelo OSI vs TCP-IP]] -> Análisis de las capas de abstracción (Física, Enlace, Red, Transporte, Aplicación) y mapeo del flujo de datos.
* [[02_Encapsulamiento de Datos]] -> El viaje de la información: de Datos a Segmentos (Capa 4), Paquetes (Capa 3), Tramas (Capa 2) y Bits (Capa 1).

## 🔀 2. Direccionamiento y Enrutamiento (Capa de Red)
* [[03_Direccionamiento IP y Subnetting]] -> Estructura de direcciones IPv4/IPv6, cálculo de máscaras de subred (CIDR), asignación de rangos útiles y redes públicas vs. privadas (RFC 1918).
* [[04_Protocolos de Enrutamiento y Gateway]] -> Mecanismos de salto, funcionamiento de la Puerta de Enlace (Gateway), tablas de enrutamiento y protocolos esenciales (ARP, ICMP, enrutamiento estático y dinámico).

## 🚚 3. Protocolos de Transporte y Conexión
* [[05_TCP vs UDP]] -> Comparativa técnica entre transporte orientado a conexión, confiable y con control de flujo (TCP) frente a transporte no orientado a conexión y de baja latencia (UDP).
* [[06_El Apretón de Manos de Tres Vías (Three-Way Handshake)]] -> Análisis a nivel de bits de las banderas TCP (`SYN`, `SYN-ACK`, `ACK`) para el establecimiento, mantenimiento y finalización (`FIN`, `RST`) de sesiones de comunicación.

## 🔑 4. Servicios de Red Esenciales
* [[07_DNS (Domain Name System)]] -> La arquitectura de resolución de nombres, jerarquía de servidores raíz y TLDs, y análisis de registros reales (`A`, `AAAA`, `CNAME`, `MX`, `TXT`, `PTR`).
* [[08_DHCP (Dynamic Host Configuration Protocol)]] -> El proceso de asignación dinámica de red conocido como el intercambio `DORA` (Discover, Offer, Request, Acknowledge).
* [[09_Protocolos de Capa de Aplicación]] -> Funcionamiento técnico y análisis de cabeceras de protocolos estándar: HTTP/HTTPS (Puertos 80/443), SSH (Puerto 22), FTP (Puertos 20/21), SMB (Puerto 445).

## 🔍 5. Herramientas de Diagnóstico y Análisis de Tráfico
* [[10_Herramientas de Red CLI (Ping, Traceroute, Netstat)]] -> Comandos nativos del sistema para verificar conectividad, trazar rutas de red y listar conexiones activas asociadas a procesos.
* [[11_Introducción a Wireshark y Tshark]] -> Captura de tráfico en interfaces de red, aplicación de filtros de visualización (BPF) y análisis forense de paquetes en archivos `.pcap`.

---
*MOCs Relacionados:* [[MOC_Fundamentos_Sistemas]][[MOC_Metodologias]]