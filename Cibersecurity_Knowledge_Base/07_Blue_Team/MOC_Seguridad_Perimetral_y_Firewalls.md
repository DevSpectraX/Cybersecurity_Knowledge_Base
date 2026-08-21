---
tags: [ciberseguridad, moc, blue_team, red_team, defensiva, redes, indice]
---
# MOC: Seguridad Perimetral y Firewalls

Este **Map of Content (MOC)** actúa como índice central para la documentación de tecnologías de cortafuegos, mecanismos de filtrado de tráfico en capas 3, 4 y 7, arquitecturas de segmentación de red y las correspondientes metodologías de auditoría/evasión desde la perspectiva de auditoría de seguridad.

---

## 🛡️ 1. Tipologías y Mecanismos de Filtrado

* **[[Firewalls Stateless vs Stateful]]**: Comparativa técnica entre el filtrado estático de paquetes sin estado (L3/L4) y el seguimiento dinámico de tablas de estado de conexión (*State Table*).
* **[[Next-Generation Firewalls (NGFW)]]**: Análisis de cortafuegos de nueva generación con inspección profunda de capa de aplicación (App-ID), identificación de usuarios (User-ID) y prevención de amenazas integrada.
* **[[Web Application Firewall (WAF)]]**: Inspección especializada en la capa de aplicación (HTTP/HTTPS) orientada a la mitigación de vulnerabilidades web (OWASP Top 10).

---

## 🏗️ 2. Arquitecturas y Segmentación de Red

* **[[DMZ y Arquitecturas de Segmentación]]**: Diseño de zonas desmilitarizadas, bastionado de red perimetral, políticas de tráfico por defecto (*Default Deny*) y aislamiento de redes de producción.
* **[[El Modelo Zero Trust]]**: Principios de microsegmentación de red, verificación explícita, control de accesos basado en identidades y eliminación del perímetro de confianza implícito.

---

## 🛠️ 3. Implementaciones Técnicas y Herramientas

* **[[iptables y nftables]]**: Configuración y administración del cortafuegos nativo del kernel de Linux (Netfilter), incluyendo la gestión de tablas, cadenas, reglas y sintaxis de línea de comandos.
* **[[pfsense y OPNsense]]**: Despliegue y administración de soluciones perimetrales integradas *Open Source* basadas en FreeBSD.

---

## 🥷 4. Perspectiva Ofensiva y Auditoría (Red Team)

* **[[Técnicas de Bypasseo de Firewall]]**: Métodos de evasión mediante encapsulación de tráfico, uso de puertos de salida permitidos (*Egress Filtering Bypass*), fragmentación y canalización a través de proxys.
* **[[Evasión de WAF]]**: Técnicas de ofuscación de payloads web (codificaciones anidadas, variaciones sintácticas, delimitadores nulos y fragmentación HTTP).

---

*MOCs de Referencia Relacionados:*

* [[MOC_IDS_IPS]]
* [[MOC_Fundamentos_Redes]]