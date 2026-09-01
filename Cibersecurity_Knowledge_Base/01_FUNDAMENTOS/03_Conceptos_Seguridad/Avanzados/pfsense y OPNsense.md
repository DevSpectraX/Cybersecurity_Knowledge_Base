---

tags: [ciberseguridad, pfsense, opnsense, freebsd, firewalls, open_source, blue_team, defensiva]
---
# pfsense y OPNsense

**pfSense** y **OPNsense** son las dos distribuciones de seguridad perimetral *Open Source* orientadas a entornos corporativos más populares del ecosistema. Ambas están basadas en el sistema operativo **FreeBSD** y utilizan el filtro de paquetes nativo del kernel **PF (Packet Filter)** para gestionar cortafuegos de inspección de estado (*Stateful*), enrutamiento avanzado, redes privadas virtuales (VPN) y servicios unificados de gestión de amenazas (UTM).

---

## 🏗️ 1. Origen y Arquitectura Interna

Ambas soluciones comparten un ancestro común (**m0n0wall**), pero divergieron significativamente en su filosofía de desarrollo, gobernanza e interfaz de administración:

```text
                     ┌──────────────────┐
                     │     m0n0wall     │
                     └────────┬─────────┘
                              │
               ┌──────────────┴──────────────┐
               ▼                             ▼
      ┌────────────────┐            ┌────────────────┐
      │    pfSense     │            │    OPNsense    │
      │ (Netgate / BSD)│            │ (Deciso / BSD) │
      └────────────────┘            └────────┬───────┘
                                             │ (Fork 2015)
                                             ▼
                                    [ Rediseño Modular ]

```

### El Motor de Filtrado: Packet Filter (PF)

A diferencia de Linux (que utiliza Netfilter/nftables), FreeBSD confía en **PF**, un cortafuegos con estado caracterizado por una sintaxis de reglas altamente estructurada, un motor de *shaping* de tráfico muy eficiente y un manejo optimizado de tablas de direcciones IP en memoria.

---

## 📊 2. Comparativa Técnica: pfSense vs. OPNsense

| Característica | pfSense (Community / Plus) | OPNsense |
| --- | --- | --- |
| **Gobernanza y Licencia** | Controlado por **Netgate**. Modelo comercial *Freemium* / Código parcialmente cerrado en ediciones Plus. | Desarrollado por **Deciso**. Modelo 100% *Open Source* (Licencia BSD de 2 cláusulas). |
| **Interfaz de Usuario (GUI)** | Basada en código legacy (PHP estático). Funcional pero menos moderna. | Arquitectura web modular moderna basada en **Phalcon (MVC / REST API)**. |
| **Actualizaciones y Código** | Ciclo de lanzamientos irregular orientado a versiones estables corporativas. | Ciclos de actualización predecibles de 2 veces al año (Enero/Julio) + parches quincenales. |
| **Módulos UTM e Integración** | Integra **Snort** y **Suricata** como paquetes adicionales. | Soporta **Suricata** de forma nativa e integra el motor de categorización web **Zenarmor (Sensei)**. |
| **API de Automatización** | Limitada/Propietaria en versiones recientes sin paquetes de terceros. | **API REST nativa completa** para integración con herramientas de orquestación (Ansible, Terraform). |

---

## ⚙️ 3. Servicios Perimetrales Clave e Integraciones

Ambas plataformas permiten transformar un hardware x86 en un aparato de seguridad perimetral completo mediante la activación de los siguientes paquetes:

1. **Prevención de Intrusiones (IDS/IPS):** Integración nativa de motores como *Suricata* o *Snort*, permitiendo la inspección profunda de paquetes (DPI) en la interfaz WAN o LAN con bloqueo automático de IPs maliciosas mediante tablas de PF.
2. **Servicios VPN (Acceso Remoto y Site-to-Site):** Soporte nativo para **WireGuard**, **OpenVPN** e **IPsec** con aceleración criptográfica por hardware (AES-NI).
3. **Alta Disponibilidad (HA):** Redundancia de cortafuegos mediante los protocolos **CARP** (*Common Address Redundancy Protocol*) para IP virtual compartida y **pfsync** para la replicación continua de la tabla de estados entre dos nodos.

---

## 🥷 4. Perspectiva de Red Team: Vector de Auditoría y Post-Explotación

Durante una auditoría perimetral o evaluación interna, la presencia de un nodo pfSense/OPNsense ofrece oportunidades específicas de reconocimiento y post-explotación si el dispositivo presenta errores de configuración:

### 1. Detección de Interfaces de Administración Expuestas (WAN)

Por defecto, ambas soluciones bloquean todo el tráfico entrante en la interfaz WAN y restringen el panel de administración a la red LAN.

* **Vulnerabilidad / Misconfiguration:** Si un administrador abre el puerto web de gestión (80/443 TCP) o el acceso SSH (22 TCP) hacia Internet para soporte remoto, la interfaz de login revela la versión exacta en la cabecera HTTP o respuestas de error, permitiendo la búsqueda de vulnerabilidades conocidas (ej. ejecuciones de comandos autenticadas o fallos de inyección en plugins de terceros).

### 2. Extracción de Credenciales y Claves VPN (Acceso Local / Root)

Si el auditor obtiene acceso local (vía consola, SSH con credenciales débiles o RCE en un paquete instalado):

* **Archivo de Configuración Unificado:** Tanto pfSense como OPNsense almacenan **toda la configuración del sistema en un único archivo XML**:
* pfSense: `/conf/config.xml`
* OPNsense: `/conf/config.xml`


* **Impacto:** Este archivo contiene en texto claro o mediante hashes descifrables:
* Usuarios y hashes de contraseñas de la interfaz web.
* Certificados digitales, claves privadas de CA y claves precompartidas (PSK) de los túneles **IPsec / OpenVPN / WireGuard**.
* Claves de integración con el Directorio Activo / LDAP.



```bash
# Comando de auditoría local para buscar secretos en el archivo de configuración
grep -iE "(password|binddn|pre-shared-key|private-key)" /conf/config.xml

```

*MOC de Referencia:* [[MOC_Seguridad_Perimetral_y_Firewalls]]