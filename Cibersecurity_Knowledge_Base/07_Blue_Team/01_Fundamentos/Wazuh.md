---
tags: [ciberseguridad, hids, hips, wazuh, siem, xdr, ossec, blue_team, auditoria]
---
# Wazuh

**Wazuh** es una plataforma de seguridad de código abierto (*Open Source*) utilizada para la detección de amenazas, la monitorización de la integridad de archivos, la protección de endpoints y la respuesta ante incidentes (XDR/SIEM). Nació originalmente como un *fork* del motor HIDS **OSSEC**, evolucionando hacia una solución integral escalable capaz de analizar telemetría en tiempo real a nivel de host y de red.

Funciona mediante la instalación de **agentes ligeros** en los endpoints (Linux, Windows, macOS, Solaris, AIX) que recopilan logs, eventos del sistema y datos de integridad para enviarlos de forma cifrada a un **servidor central** encargados de la correlación de eventos y el análisis de reglas.

## 🏗️ 1. Arquitectura de Componentes de Wazuh

El ecosistema Wazuh se despliega dividiendo las responsabilidades en tres capas principales:

| Componente | Función Técnica |
| --- | --- |
| **Wazuh Agent** | Servicio instalado en las máquinas finales. Monitoriza procesos, lee logs (`/var/log/*`, Event Log de Windows), inspecciona modificaciones en archivos (FIM) y ejecuta comandos de mitigación local (*Active Response*). |
| **Wazuh Server** | Nodo central que recibe los datos de los agentes por el puerto UDP/TCP `1514`. Decodifica los logs, procesa el motor de reglas de análisis, genera alertas y gestiona la infraestructura de los agentes (puerto `1515` para registro). |
| **Wazuh Indexer & Dashboard** | Motor de búsqueda analítico (basado en OpenSearch) que almacena las alertas indexadas y ofrece la interfaz web visual para análisis forense, dashboards de cumplimiento (PCI-DSS, NIST, MITRE ATT&CK) y gestión de incidentes. |

---

## 🛠️ 2. Capacidades Técnicas Clave

Wazuh combina funciones de **HIDS** (detección) y **HIPS** (prevención) mediante varios módulos especializados:

### 1. Monitorización de Integridad de Archivos (FIM - File Integrity Monitoring)

El módulo `syscheck` escanea directorios críticos (`/etc`, `/bin`, `C:\Windows\System32`) calculando hashes criptográficos (MD5, SHA-1, SHA-256).

* **Mecanismo:** Utiliza llamadas al kernel del sistema operativo (`inotify` en Linux, `ReadDirectoryChangesW` en Windows) para detectar modificaciones, creaciones, borrados o cambios de permisos en archivos en tiempo real.

### 2. Detección de Vulnerabilidades y Mal配置 (SCA)

El motor analiza periódicamente el inventario de aplicaciones instaladas en los hosts comparándolo contra bases de datos públicas de **CVE** (Common Vulnerabilities and Exposures), alertando sobre paquetes de software desactualizados o vulnerables. El módulo **SCA** (*Security Configuration Assessment*) valida el cumplimiento de guías de bastionado (*hardening* CIS Benchmarks).

### 3. Respuesta Activa (*Active Response* - Capacidad HIPS)

Cuando el servidor de Wazuh procesa un evento que supera un nivel de severidad determinado (configurado en el archivo `ossec.conf`), puede ordenar automáticamente al agente la ejecución de una acción defensiva local:

* **Linux:** Ejecutar scripts para bloquear direcciones IP en el cortafuegos local mediante `iptables`/`nftables` o añadir entradas a `/etc/hosts.deny`.
* **Windows:** Ejecutar comandos en PowerShell para deshabilitar cuentas de usuario comprometidas, finalizar procesos sospechosos o aislar la máquina de la red.

---

## 📝 3. Estructura de Reglas y Decodificadores en Wazuh

Wazuh procesa la información en dos fases: primero pasa el log por un **Decodificador** (extrae variables como IP origen, usuario, puerto) y luego por el **Motor de Reglas** (formato XML).

### Ejemplo de Regla Personalizada (XML): Detección de Modificación en `/etc/passwd`

```xml
<group name="syscheck,pivoting_detection,">
  <rule id="100005" level="10">
    <if_sid>550</if_sid>
    <field name="file">/etc/passwd</field>
    <description>ALERTA CRITICA: Modificacion detectada en el archivo de usuarios /etc/passwd</description>
    <mitre>
      <id>T1078</id>
    </mitre>
  </rule>
</group>

```

* **`id="100005"`:** Identificador único de la regla personalizada (las reglas custom deben usar IDs entre `100000` y `120000`).
* **`level="10"`:** Nivel de severidad en la escala de Wazuh (del 0 al 16). Un nivel 10 o superior se considera una amenaza grave.
* **`<if_sid>550</if_sid>`:** Regla heredada. La regla `550` es la regla genérica de Wazuh para alertas del módulo FIM (*File Modified*).

---

## 🥷 4. Perspectiva de Red Team: Evasión y Limitaciones de Wazuh

Auditar un endpoint protegido por un agente de Wazuh requiere comprender los vectores de evasión en el sistema host:

1. **Ataques Fileless / Inyección en Memoria:** Wazuh FIM monitoriza cambios escritos físicamente en el disco. Las ejecuciones en memoria (*Reflective DLL Injection*, ejecución de shellcode vía Process Injection) no generan eventos de modificación de archivos en el disco duro.
2. **Desactivación de Telemetría (con privilegios de Administrator/Root):** Si el atacante consigue acceso con máximos privilegios (`root` o `SYSTEM`), el vector directo es detener o bloquear el servicio del agente (`systemctl stop wazuh-agent` o `net stop Wazuh`), rompiendo la comunicación con el manager.
3. **Ofuscación de Cadenas en Logs:** Si una regla busca comandos específicos en los logs de auditoría (ej. `auditd` o Event Log 4688), el uso de variables de entorno u ofuscación en PowerShell (`Invoke-Expression`, codificación Base64 con `-EncodedCommand`) evita la coincidencia con decodificadores basados en texto plano.

*MOC de Referencia:* [[MOC_IDS_IPS]]