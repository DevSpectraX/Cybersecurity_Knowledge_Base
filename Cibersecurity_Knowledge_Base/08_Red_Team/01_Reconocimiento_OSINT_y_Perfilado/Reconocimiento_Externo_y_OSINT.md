---
tags: [red_team, recon, osint, enumeracion, active_recon, passive_recon]
---

## 🎯 1. Concepto Técnico y Mecánica
El reconocimiento es la fase inicial del ciclo de vida de un ataque ofensivo. Su objetivo es mapear la superficie de exposición externa de una organización sin alertar a los sistemas de defensa (SOC/SIEM). Se divide en dos metodologías principales:

*   **Reconocimiento Pasivo (OSINT):** Recopilación de información pública disponible en terceros (DNS públicos, certificados SSL/TLS, motores de búsqueda, repositorios de código, registros WHOIS) sin interactuar directamente con la infraestructura del objetivo.
*   **Reconocimiento Activo:** Interacción directa con los sistemas del objetivo (escaneo de puertos, peticiones HTTP/DNS directas, banner grabbing) generando registros de auditoría en los sistemas perimetrales.

---

## 🔍 2. Reconocimiento Pasivo (OSINT)

### A. Descubrimiento de Subdominios (Passive Subdomain Enumeration)
Consulta de registros históricos y bases de datos públicas para identificar nombres de host asociados al dominio principal.

*   **Logs de Transparencia de Certificados (CT Logs):** Los navegadores exigen que todos los certificados SSL/TLS emitidos por autoridades de certificación (CA) públicas se registren en un log público.
*   **APIs CTI y DNS Pasivo:** Servicios como VirusTotal, SecurityTrails, Censys y Shodan indexan registros DNS históricos.

### B. Búsqueda de Activos Expuestos (Shodan / Censys)
Motores de búsqueda de dispositivos conectados a Internet que escanean rangos IPv4/IPv6 de forma continua indexando servicios, banners de software y certificados.

*   **Búsquedas por ASN (Autonomous System Number):** Identificación de todos los bloques de direcciones IP pertenecientes a la organización.

### C. Fuga de Credenciales y Repositorios (GitDorks / Leaks)
Análisis de código fuente expuesto públicamente en plataformas como GitHub, GitLab o Pastebin donde los desarrolladores pueden haber incluido credenciales, tokens API (*Bearer Keys*) o endpoints privados hardcodeados.

---

## ⚡ 3. Reconocimiento Activo y Mapeo de Infraestructura

### A. Enumeración DNS Activa
Interrogación directa a los servidores de nombres (Authoritative Nameservers) de la organización mediante:

*   **Ataques de Transferencia de Zona (AXFR):** Petición al servidor DNS para que entregue la copia completa de la zona. Si el servidor está mal configurado, revela la totalidad de la infraestructura interna/externa.
*   **Fuerza Bruta DNS:** Resolución interactiva de nombres probando diccionarios masivos contra resolvers públicos o del propio objetivo.

### B. Mapeo de Puertos y Servicios (Port Scanning & Fingerprinting)
Envío de paquetes de red para determinar el estado de los puertos TCP/UDP.

*   **SYN Stealth Scan (`-sS`):** Envía un paquete TCP SYN. Si recibe `SYN-ACK`, envía un `RST` para cerrar la conexión antes de completar el handshake de 3 vías, reduciendo la probabilidad de registro en aplicaciones de capa de red básica.
*   **Probing de Servicios (`-sV`):** Envío de sondas específicas a los puertos abiertos para analizar la respuesta (*banner*) y determinar la versión exacta de software/firmware.

---

## 🛠️ 4. Guía de Ejecución y Explotación Operativa

### Paso 1: Enumeración Pasiva de Subdominios con `Amass` y `subfinder`
Ejecución de consulta pasiva agregando fuentes públicas sin tocar el objetivo:

```bash
# Enumeración pasiva con subfinder agregando fuentes API
subfinder -d objetivo.com -o subdominios_subfinder.txt

# Descubrimiento profundo con OWASP Amass (Modo Pasivo)
amass enum -passive -d objetivo.com -o subdominios_amass.txt

# Fusionar y filtrar resultados únicos
cat subdominios_subfinder.txt subdominios_amass.txt | sort -u > subdominios_totales.txt

```

### Paso 2: Filtrado de Subdominios Activos con `httpx`

Verificación de resolución DNS y disponibilidad del servicio HTTP/HTTPS:

```bash
cat subdominios_totales.txt | httpx -title -tech-detect -status-code -ip -o objetivos_vivos.txt

```

### Paso 3: Identificación de Bloques IP y Escaneo de Infraestructura con `Nmap`

Localización del ASN de la organización y escaneo de puertos de los hosts confirmados:

```bash
# Escaneo SYN silencioso de puertos TCP comunes con detección de versiones
nmap -sS -sV -Pn -p 80,443,8080,8443,22,21,3389 -iL objetivos_vivos.txt -oA escaneo_perimetro

```

### Paso 4: Fuzzing de Directorios Web con `ffuf`

Descubrimiento de rutas no enlazadas, paneles de administración y archivos sensibles:

```bash
ffuf -u [https://objetivo.com/FUZZ](https://objetivo.com/FUZZ) -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -mc 200,204,301,302,307 -o fuzzing_web.json

```

---

## 🛡️ 5. Detección, Telemetría y Mitigación (Blue Team)

* **Detección de Escaneos (IDS/IPS):** Reglas de Suricata/Snort que detectan ráfagas de paquetes TCP SYN sin completar Handshake desde un único origen hacia múltiples puertos locales.
* **Alertas de AXFR:** Logs de DNS Server registrando eventos de transferencia de zona (`Event ID 600` en Windows DNS Server o consultas de tipo `AXFR` en BIND9) rechazados o permitidos desde IPs no autorizadas.
* **Mitigación:**
	* Restringir las transferencias de zona DNS mediante listas de control de acceso (`allow-transfer { IP_SECUNDARIO; };`).
	* Implementar WAF y limitaciones de tasa (*Rate Limiting*) en endpoints para bloquear la enumeración automatizada.
	* Configurar registros de transparencia CT y monitorizar repositorios con herramientas como *TruffleHog* o *GitGuardian*.

