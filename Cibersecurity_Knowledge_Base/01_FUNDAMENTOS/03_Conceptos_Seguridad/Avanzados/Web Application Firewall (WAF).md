---
tags: [ciberseguridad, waf, owasp, web_security, firewall, red_team, blue_team]
---
# Web Application Firewall (WAF)

Un **Web Application Firewall (WAF)** es una solución de seguridad perimetral especializada en la inspección, filtrado y bloqueo del tráfico HTTP/HTTPS dirigido hacia servicios y aplicaciones web. A diferencia de los cortafuegos de red tradicionales o NGFW (que operan en las capas 3, 4 y 7 para el control de tráfico de red general), el WAF actúa específicamente como un **proxy inverso en Capa 7 (Aplicación)** para proteger el software de vulnerabilidades web de la lógica de negocio y del estándar **OWASP Top 10**.

---

## 🛡️ 1. Mecanismos de Inspección y Modelos de Seguridad

Un WAF analiza el contenido detallado de las peticiones HTTP/HTTPS (métodos, URLs, parámetros GET/POST, cabeceras, cookies y payloads en JSON/XML) utilizando dos modelos complementarios de protección:

|**Modelo de Seguridad**|**Funcionamiento Técnico**|**Ventajas y Desventajas**|
|---|---|---|
|**Modelo Negativo (Blacklisting / Firmas)**|Utiliza firmas predefinidas y expresiones regulares (_Regex_) para identificar patrones de ataques conocidos (ej. cadenas tipo `' OR 1=1 --` o `<script>alert(1)</script>`).|**Ventaja:** Despliegue rápido sin necesidad de conocer la estructura interna de la web.<br><br>  <br><br>**Desventaja:** Incapaz de detener ataques Zero-Day o bypasses por variación de sintaxis.|
|**Modelo Positivo (Whitelisting / Basado en Estado)**|Define de forma estricta la estructura exacta permitida para el tráfico legítimo (longitudes máximas de parámetro, tipos de datos esperados, métodos HTTP autorizados).|**Ventaja:** Ofrece máxima protección frente a ataques Zero-Day.<br><br>  <br><br>**Desventaja:** Elevada tasa de falsos positivos y mantenimiento complejo ante cambios de código.|

---

## 🔄 2. Arquitecturas de Despliegue

Los WAFs pueden integrarse en la infraestructura de la red mediante tres modalidades principales:

1. **Reverse Proxy (En Línea / Inline):** Todo el tráfico web entrante pasa obligatoriamente a través del WAF antes de llegar al servidor web de origen (*Backend*). Permite el bloqueo dinámico en tiempo real y la terminación SSL/TLS.
2. **Basado en la Nube / CDN (Cloud-based WAF):** El tráfico se redirige al WAF modificando los registros DNS (*CNAME*) de la aplicación. Soluciones como **Cloudflare**, **AWS WAF** o **Akamai** filtran los ataques en el perímetro de su red distribuida antes de reenviar el tráfico limpio al origen.
3. **Basado en Host / Agente (Out-of-Band / Embedded):** El módulo de WAF se instala como un plugin o middleware directamente dentro del servidor web (ej. **ModSecurity** integrado en Apache/Nginx).

---

## 💻 3. Ejemplo Práctico: Regla de ModSecurity (OWASP CRS)

Ejemplo de regla en lenguaje SecRule (utilizado por ModSecurity) para la detección de inyecciones SQL básicas mediante la búsqueda de sintaxis SQL en los parámetros recibidos:

```text
SecRule ARGS "@rx (?i)(union\s+select|select\s+.*\s+from|insert\s+into|drop\s+table)" \
    "id:100001,\
    phase:2,\
    block,\
    capture,\
    msg:'POSIBLE ATAQUE DE INYECCION SQL (SQLi) DETECTADO EN PARAMETROS HTTP',\
    logdata:'Matched Data: %{TX.0} found within %{MATCHED_VAR_NAME}: %{MATCHED_VAR}',\
    tag:'OWASP_TOP_10/A03_INJECTION',\
    severity:'CRITICAL'"

```

* **`ARGS`:** Analiza todos los parámetros recibidos en la petición HTTP (GET y POST).
* **`@rx (?i)...`:** Expresión regular insensible a mayúsculas/minúsculas para capturar palabras clave de SQL.
* **`phase:2`:** Fase de inspección correspondiente al cuerpo de la petición (*Request Body*).

---

## 🥷 4. Perspectiva de Red Team: Evasión de WAF (WAF Bypass)

Durante la fase de explotación web en un ejercicio de auditoría, las firmas del WAF se pueden evadir manipulando la sintaxis del payload sin alterar su ejecución lógica en la base de datos o intérprete:

### 1. Codificación Anidada y Transformación de Caracteres

Si el WAF solo aplica un nivel de descodificación URL o no interpreta ciertos formatos, el payload puede estructurarse con doble codificación (*Double URL Encoding*), Unicode o representación Hexadecimal.

* **Payload Original:** `<script>alert(1)</script>`
* **Bypass por Doble Codificación:** `%253Cscript%253Ealert(1)%253C%252Fscript%253E`

### 2. Ofuscación de Sintaxis y Comentarios Inline (SQLi)

Aprovechar las particularidades del motor SQL de destino (MySQL, PostgreSQL, MSSQL) para romper las firmas de expresiones regulares basadas en espacios en blanco.

* **Firma Bloqueada:** `SELECT * FROM users WHERE id = 1 UNION SELECT 1,2,3`
* **Payload de Evasión (MySQL):** `UNIQUE/**/SELECT/*!--+?*/1,username,password/**/FROM/**/users`

### 3. Contaminación de Parámetros HTTP (HTTP Parameter Pollution - HPP)

Enviar múltiples parámetros con el mismo nombre en la petición HTTP (`?id=1&id=2&id=UNION SELECT`). Dependiendo de la tecnología del backend (ASP.NET, PHP, Node.js), la aplicación concatenará los valores, mientras que el WAF puede evaluar únicamente el primer parámetro, pasando el ataque por alto.

*MOC de Referencia:* [[MOC_Seguridad_Perimetral_y_Firewalls]]