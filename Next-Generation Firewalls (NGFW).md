---
tags: [ciberseguridad, firewalls, ngfw, dpi, appid, userid, blue_team, defensiva]
---
# Next-Generation Firewalls (NGFW)

Un **Next-Generation Firewall (NGFW)** o Cortafuegos de Nueva Generación es una solución de seguridad perimetral que evoluciona el filtrado de puerto y protocolo tradicional (Capas 3 y 4 del modelo OSI) mediante la integración de **inspección profunda de paquetes (DPI) en Capa 7**, prevención de intrusiones (IPS), control de aplicaciones y vinculación de identidades de usuario.

A diferencia de los cortafuegos *Stateful* convencionales, que confían en que el tráfico en el puerto TCP 80/443 es navegación web estándar, un NGFW clasifica el tráfico basándose en la **firma real de la aplicación** (*App-ID*), independientemente del puerto o protocolo utilizado.

---

## 🏗️ 1. Capacidades Clave y Pilares Tecnológicos

Los NGFW de fabricantes líderes (Palo Alto Networks, Fortinet FortiGate, Check Point) integran tres motores principales de análisis:

| Componente / Pilar | Función Técnica | Impacto en la Seguridad |
| --- | --- | --- |
| **Identificación de Aplicaciones (App-ID)** | Decodifica la capa de aplicación para identificar el comportamiento exacto del software (ej. diferenciar entre el uso básico de *SSL/TLS*, *Tor*, *TeamViewer* o *SSH* corriendo sobre el puerto 80). | Evita el bypass de filtrado por puerto. Si una aplicación C2 intenta usar el puerto 443 pero no realiza un apretón de manos HTTPS legítimo, es bloqueada. |
| **Integración con Identidad (User-ID)** | Vincula las direcciones IP dinámicas con identidades de usuario reales mediante integración con Active Directory (Kerberos/NTLM), LDAP o Captive Portals. | Permite aplicar políticas basadas en roles de usuario/grupo (ej. *"Permitir acceso a Git solo al grupo de Desarrolladores"*) en lugar de reglas basadas en subredes IP. |
| **Inspección SSL/TLS (Decryption)** | Actúa como un proxy Man-in-the-Middle (MitM) transparente descifrando y re-cifrando el tráfico HTTPS en tiempo real para analizar su contenido interno. | Permite al motor de IPS y Antivirus inspeccionar amenazas que viajan dentro de sesiones cifradas (que representan más del 80% del tráfico web actual). |

---

## 🧱 2. NGFW vs. Cortafuegos Conectivo Tradicional

```text
[Paquete IP/TCP] 
       │
       ├──► Firewall Tradicional ──► ¿Puerto 443 abierto? ──► PERMITIR (Ciego al contenido)
       │
       └──► NGFW (Capa 7) ─────────► Decodifica SSL/TLS ──► ¿Es tráfico HTTPS legítimo? 
                                                          ├─► No (Es un C2 sobre SSL) ──► BLOQUEAR
                                                          └─► Sí ──► Análisis IPS/AV ──► PERMITIR

```

### Diferencias Operativas:

* **Tradicional (Stateful):** Toma decisiones basadas en `[IP Origen : Puerto Origen] -> [IP Destino : Puerto Destino]`. No analiza si el contenido del tráfico HTTP contiene un *malware* o si la conexión la realiza un troyano.
* **NGFW:** Evalúa la combinación `[Usuario] + [Aplicación] + [Contenido/Amenaza]`. Puede permitir la aplicación "Google Drive" para lectura, pero bloquear la función de "subida de archivos" para evitar fuga de información (DLP).

---

## 🥷 3. Perspectiva de Red Team: Desafíos y Evasión en Entornos NGFW

Auditar un perímetro protegido por un NGFW requiere superar sus controles de Capa 7 e inspección de contenido:

### 1. Evasión de App-ID mediante Imitación de Protocolo (Protocol Mimetism)

Dado que el NGFW analiza los primeros paquetes del flujo para clasificar la aplicación, el software de C2 (*Command and Control*) o las herramientas de exfiltración deben imitar fielmente la estructura y cabeceras de un protocolo legítimo.

* **Técnica de Auditoría:** Utilizar perfiles de maleabilidad (*Malleable C2 profiles*) en frameworks como Cobalt Strike o Sliver para estructurar las peticiones HTTP/S exactamente como lo haría un navegador web real o servicios como Amazon/Google, forzando al NGFW a clasificar la conexión como tráfico seguro.

### 2. Explotación de la Falta de Inspección SSL/TLS

Descifrar tráfico TLS requiere una capacidad de cómputo elevada (aceleración por hardware). Muchas organizaciones no activan la **inspección SSL/TLS** por motivos de rendimiento, privacidad o falta de certificados CA desplegados en las máquinas cliente.

* **Técnica de Auditoría:** Canalizar todas las herramientas de explotación, reverse shells o comandos de exfiltración dentro de sesiones TLS válidas con certificados legítimos (ej. *Let's Encrypt*). Si el NGFW no realiza inspección MitM, no podrá aplicar las firmas de IPS/Antivirus al payload cifrado.

*MOC de Referencia:* [[MOC_Seguridad_Perimetral_y_Firewalls]]