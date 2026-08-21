---

tags: [ciberseguridad, hids, hips, wazuh, ossec, siem, blue_team, auditoria]
---
# HIDS y HIPS (Host-based Intrusion Detection / Prevention Systems)

Un **HIDS** (Sistema de Detección de Intrusiones en Host) y un **HIPS** (Sistema de Prevención de Intrusiones en Host) son soluciones de seguridad instaladas directamente dentro del sistema operativo objetivo (endpoints, servidores, máquinas virtuales o contenedores).

A diferencia de las soluciones de red (NIDS/NIPS), un HIDS/HIPS no depende de la inspección del tráfico del cable, sino que analiza la actividad interna de la máquina: logs del sistema, integridad de archivos críticos, modificaciones en el registro, llamadas al sistema (*syscalls*) y conexiones activas. Esto le permite detectar amenazas incluso si el tráfico de red está completamente cifrado vía TLS/SSL.

## 🏗️ 1. Arquitectura de Funcionamiento y Componentes

Un sistema HIDS/HIPS moderno (como **Wazuh** u **OSSEC**) opera mediante un modelo **Cliente-Servidor (Agente-Manager)**:

### 1. El Agente (En el Endpoint / Host)

Es un servicio o demonio de bajo consumo que se ejecuta en el sistema operativo objetivo (Linux, Windows, macOS, UNIX).

* **Recolector de Telemetría:** Lee eventos del sistema en tiempo real (archivos en `/var/log/*`, registros de Windows Event Log, auditoría del kernel con `auditd`).
* **Monitor de Integridad de Archivos (FIM):** Monitoriza carpetas críticas (`/etc/`, `/bin/`, `C:\Windows\System32`) mediante el cálculo periódico de hashes (SHA-256) o la API del kernel (`inotify` en Linux) para alertar si un archivo es modificado o sustituido.
* **Detección de Rootkits:** Escanea el sistema en busca de interfaces en modo promiscuo, puertos ocultos, inconsistencias en llamadas a `/proc` o firmas de malware conocido.

### 2. El Manager (Servidor Central / SIEM)

Recibe la telemetría enviada por los agentes de forma cifrada (puerto UDP/TCP 1514 en Wazuh/OSSEC).

* **Motor de Decodificación y Reglas:** Normaliza los logs entrantes y los pasa por un árbol de reglas XML para calcular el nivel de severidad de la alerta (escalas del 0 al 15).
* **Correlación de Eventos:** Relaciona múltiples alertas de distintos hosts (por ejemplo, detectar un ataque de fuerza bruta SSH fallido en 10 servidores desde la misma IP externa).

---

## 🛠️ 2. El Ecosistema Wazuh (El Estándar HIDS / XDR Open Source)

**Wazuh** es la evolución directa del proyecto Open Source OSSEC. Se ha consolidado como el referente de la industria para implementar capacidades de HIDS, XDR (Extended Detection and Response) y SIEM unificado.

El ecosistema Wazuh se compone de tres módulos principales:

| Componente | Función Técnica |
| --- | --- |
| **Wazuh Agent** | Agente ligero desplegado en los hosts para extracción de logs, FIM, inventario de software (SCA) y detección de vulnerabilidades. |
| **Wazuh Server** | Motor encargado de procesar los datos de los agentes, aplicar el motor de reglas de análisis y gestionar las respuestas activas. |
| **Wazuh Indexer & Dashboard** | Motor de búsqueda y analítica (basado en OpenSearch) con interfaz web para visualización de alertas, dashboards e informes de cumplimiento (PCI-DSS, NIST, MITRE ATT&CK). |

### ⚡ Respuesta Activa (Capacidad HIPS en Wazuh)

Wazuh pasa de ser un mero HIDS (detección) a actuar como un **HIPS (prevención)** mediante su módulo de **Active Response**.

Cuando el Manager detecta que se ha disparado una regla de nivel alto (por ejemplo, 5 intentos fallidos de login por SSH), el Manager le ordena automáticamente al Agente del host afectado ejecutar un script ejecutable local:

* **En Linux:** Añadir la IP atacante a `/etc/hosts.deny` o crear una regla dinámica de bloqueo en el firewall interno mediante `iptables` / `nftables`.
* **En Windows:** Bloquear la cuenta de usuario comprometida en Active Directory o añadir un bloqueo en el Windows Defender Firewall.

---

## 🥷 3. Perspectiva de Red Team: Limitaciones y Evasión de HIDS

Auditar un host con un agente HIDS/HIPS requiere comprender sus puntos ciegos a nivel del sistema operativo:

### 1. Inyección en Memoria / Fileless Malware

Los módulos FIM (File Integrity Monitoring) detectan cambios de archivos escritos en el disco duro. Si el atacante utiliza técnicas de ejecución puramente en memoria (*Fileless Attack*, inyección de DLLs o uso de *Reflective PE Injection*), no hay escritura en disco y el FIM no genera ninguna alerta.

### 2. Cegera por Apagado de Telemetría o Evasión de Auditd

Si un atacante consigue escalar privilegios hasta `root` o `SYSTEM`, su primer objetivo suele ser detener el servicio del agente (`systemctl stop wazuh-agent`) o manipular la configuración del subsistema de auditoría del kernel (`auditctl -e 0`), dejando al HIDS ciego antes de iniciar la exfiltración.

*MOC de Referencia:* [[MOC_IDS_IPS]]