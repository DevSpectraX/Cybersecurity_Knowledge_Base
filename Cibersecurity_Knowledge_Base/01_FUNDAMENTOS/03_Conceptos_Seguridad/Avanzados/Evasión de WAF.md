---
tags: [ciberseguridad, evasion_waf, waf, web_security, red_team, owasp, obfuscation]
---
# Evasión de WAF (WAF Bypass)

La **evasión de WAF (WAF Bypass)** agrupa el conjunto de técnicas, transformaciones de sintaxis y manipulaciones del protocolo HTTP utilizadas para eludir la detección de un Web Application Firewall. Dado que el WAF analiza tráfico en **Capa 7 (Aplicación)**, estas técnicas explotan las diferencias en la forma en que el WAF y el servidor web de destino (_Backend_) interpretan, descodifican o procesan la misma petición.

  

## 🛠️ 1. Principales Vectores de Evasión
  

### 1. Codificación y Transformación

Aprovecha las discrepancias en cómo el WAF y el servidor backend **normalizan y descodifican los datos**. Si el WAF no analiza el payload en el mismo formato en que lo procesará el backend, la firma falla.

  

- **Doble Codificación (Double URL Encoding):**
    
      
    - **Mecanismo:** El cliente envía la petición codificada dos veces. El WAF realiza una sola pasada de decodificación, ve un string inocuo y lo aprueba. El backend (por ejemplo, PHP, ASP.NET) aplica una segunda pasada automática y ejecuta el ataque.
        
          
        
    - **Ejemplo:** Para colar un menor que `<` (`%3C`):
        
          
        
        $$\text{Payload} \xrightarrow{\text{1ª Codificación}} \%3C \xrightarrow{\text{2ª Codificación}} \%253C$$
        
        El WAF ve `%3C` (un texto), pero el backend lo convierte finalmente en `<`.
        
          
        
- **Transformaciones Unicode y Mapeo UTF-8:**
    
      
    - **Mecanismo:** El estándar Unicode tiene múltiples representaciones para un mismo carácter (_Best-Fit Mapping_ o _Overlong UTF-8_). Si el WAF no conoce la tabla de traducción del backend, no detecta el carácter malicioso.
        
          
        
    - **Ejemplo:** En ciertos entornos IIS/ASP.NET, la secuencia Overlong `%C0%AE` es interpretada por el servidor como un punto `.`, permitiendo ataques de _Path Traversal_ (`..%C0%AE/..%C0%AE/`).
        
          
        

### 2. Ofuscación de Sintaxis (SQLi / XSS / RCE)

Aprovecha la flexibilidad de los lenguajes de programación y motores de bases de datos. Consiste en **romper la firma basada en expresiones regulares (_Regex_)** sin alterar la lógica de ejecución del intérprete.

  

- **Inserción de Comentarios y Caracteres Nulos:**
    
      
    - **SQLi:** Los motores SQL permiten comentarios inline (`/**/`) en lugar de espacios. Si la firma del WAF busca `UNION SELECT`, se esquiva mediante:
        
          
        
        ```SQL
        UNION/**/SELECT/**/username,password/**/FROM/**/users
        ```
        
    - **SQLi MySQL (Versioned Comments):** Comentarios ejecutables en versiones específicas:
        
        
        
        ```SQL
        /*!50000UNION*//*!50000SELECT*/ 1,2,3
        ```
        
- **Alternancia de Sintaxis y Payloads Esotéricos (XSS/RCE):**
    
      
    - **XSS sin etiquetas `<script>`:** Usar vectores de eventos HTML5 o etiquetas poco comunes donde el WAF no suele aplicar reglas estrictas:
        
        
        ```Html
        <body onload=alert(1)>
        <svg/onload=eval(atob('YWxlcnQoMSk='))>
        ```
        
    - **RCE mediante Variables en Bash:** Romper cadenas de comandos en Linux insertando comillas vacías o variables no definidas:
        
          
        
        ```Bash
        cat /et'c'/pas''swd
        u'n'a'm'e -a
        ```
        

## 3. Contaminación de Parámetros HTTP (HPP - HTTP Parameter Pollution)

Explota el comportamiento no estandarizado del servidor web cuando recibe **múltiples parámetros con el mismo nombre** en la misma petición (`?id=1&id=2`).

  

- **Mecanismo de Desconexión WAF vs. Backend:**
    
      
    

|**Tecnología Backend**|**Comportamiento con Parámetros Duplicados (?id=val1&id=val2)**|
|---|---|
|**ASP.NET / IIS**|Concadena los valores con coma: `val1,val2`|
|**PHP / Apache**|Toma únicamente el **último** valor: `val2`|
|**JSP / Tomcat**|Toma únicamente el **primer** valor: `val1`|

- **Vector de Ataque:**
    
    Si el atacante envía:
    
    
    ```HTTP
    GET /search.aspx?select=1&select=UNION SELECT 1,2,3 FROM users
    ```
    
    - El WAF analiza cada parámetro por separado (`select=1` y `select=UNION...`) y no encuentra un patrón SQLi completo en ninguno de los dos.
        
          
        
    - ASP.NET recibe la petición y los une: `select=1,UNION SELECT 1,2,3 FROM users`, **ejecutando la inyección SQL en la base de datos**.
        
          
        

## 4. Incoherencia de Protocolo e IP (Protocol & Infrastructure Bypass)

Afecta directamente a la arquitectura de red y la infraestructura sobre la que corre el WAF, en lugar de modificar la sintaxis del payload.

  

- **Descubrimiento de la IP de Origen (Origin IP Bypass):**
    
      
    - **Mecanismo:** Los WAFs basados en la nube (Cloudflare, AWS WAF, Akamai) actúan como un filtro DNS intermediario. Si un atacante descubre la IP pública real (_Origin IP_) del servidor backend, puede enviar sus peticiones directamente a esa IP saltándose el WAF por completo.
        
          
        
    - **Técnicas de Descubrimiento:**
        
          
        - Historial de registros DNS (_PassiveTotal_, _SecurityTrails_) antes de implementar el WAF.
            
              
            
        - Certificados SSL/TLS (búsquedas en _Censys_ o _Shodan_ analizando el _Subject Alternative Name_).
            
              
            
        - Provocar que el servidor web envíe una conexión saliente (ej. subida de avatar vía URL o notificación webhook) para registrar la IP de origen en el servidor del atacante.
            
              
            
- **Mismatches en HTTP Request Smuggling (CL.TE / TE.CL):**
    
      
    - **Mecanismo:** Manipular las cabeceras `Content-Length` (CL) y `Transfer-Encoding: chunked` (TE) en peticiones HTTP/1.1 para que el WAF y el servidor backend delimiten el final de la petición de forma diferente.
        
          
        
    - **Resultado:** El WAF piensa que está procesando una sola petición limpia, mientras que el backend procesa una segunda petición maliciosa "contrabandeada" (_smuggled_) en el cuerpo de la primera.
        
          
        

_MOC de Referencia:_ [[MOC_Seguridad_Perimetral_y_Firewalls]]

|**Técnica**|**Mecanismo de Evasión**|**Ejemplo / Payload**|
|---|---|---|
|**Doble Codificación (Double URL Encoding)**|Si el WAF solo aplica un nivel de descodificación URL y el backend descodifica dos veces, la firma maliciosa pasa oculta.|`%253Cscript%253E` _(El WAF ve `%3Cscript%3E`, el backend interpreta `<script>`)_.|
|**Comentarios e Inserción de Caracteres**|Interrumpe palabras clave detectadas por firmas Regex aprovechando particularidades del intérprete SQL/HTML.|`UNI/**/ON SELECT` o `<script>` con caracteres nulos (`%00`).|
|**Contaminación de Parámetros (HPP)**|Enviar el mismo parámetro varias veces; el WAF analiza el primer valor y el backend procesa el último (o los concatena).|`?id=1&id=UNION SELECT 1,2,3`|
|**Bypass de IP Directa (Origin IP)**|En WAFs basados en la nube (Cloudflare/AWS), conectar directamente a la IP real del servidor web omitiendo el DNS filtrado.|Modificar archivo `/etc/hosts` con la IP real del backend obtenida en historial DNS o certificados SSL.|

## 💻 2. Ejemplos Prácticos de Payloads

### 1. Inyección SQL (SQLi)

- **Payload estándar (Bloqueado):** `' OR 1=1 --`
    
      
    
- **Bypass por comentarios y caracteres especiales:** `'/**/OR/**/1/*!50000=*/1#`
    
      
    
- **Bypass por alternancia de mayúsculas/minúsculas y espacios:** `'%0aUNION%0aSELECT%0a1,group_concat(table_name)%0afrom%0ainformation_schema.tables`
    
      
    

### 2. Cross-Site Scripting (XSS)

- **Payload estándar (Bloqueado):** `<script>alert(1)</script>`
    
      
    
- **Bypass mediante eventos sin etiquetas `<script>`:** `<svg/onload=confirm(1)>`
    
      
    
- **Bypass usando Unicode / Hexadecimal:** `<iframe src="javascript:\x61\x6C\x65\x72\x74(1)">`
    
      
    

## 🛡️ 3. Contramedidas para el Blue Team

1. **Normalización Estricta:** Configurar el WAF para aplicar decodificación recursiva y normalización de caracteres (Unicode, Hex, URL) antes de evaluar las reglas de firmas.
    
      
    
2. **Ocultamiento de la IP de Origen:** Configurar el Firewall de Red (_Security Groups/iptables_) del servidor web para aceptar peticiones **únicamente desde los rangos IP del WAF**.
    
      
    
3. **Modelo de Seguridad Positivo (Whitelisting):** Definir listas blancas estrictas para tipos de datos, formatos JSON/XML y campos de parámetros esperados.
    
      
    

_MOC de Referencia:_ [[MOC_Seguridad_Perimetral_y_Firewalls]]