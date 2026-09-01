---
tags: [red_team, recon, osint, identity_profiling, surface_mapping, initial_access]
---


## 🎯 1. Concepto Técnico y Mecánica
El perfilado de identidad y superficie de ataque abarca la recolección, estructuración y validación de entidades asociadas a una organización (empleados, estructuras departamentales, formatos de correo, pasarelas de autenticación y tecnologías expuestas). 

Su objetivo principal es construir la base de datos necesaria para fases operativas posteriores:
*   **Ataques de Fuerza Bruta / Password Spraying:** Probar contraseñas comunes contra pasarelas corporativas expuestas (Single Sign-On, VPNs, Microsoft 365, Okta).
*   **Ingeniería Social e Identity Phishing (AiTM):** Diseñar escenarios personalizados dirigidos a roles específicos (RRHH, TI, Finanzas) utilizando infraestructura de suplantación.

---

## 🔍 2. Mecanismos de Recolección y Mapeo

### A. Reconocimiento de Identidad Corporativa (Corporate OSINT)
Fase pasiva orientada a descubrir nombres, apellidos, cargos y direcciones de correo electrónico de empleados activos mediante fuentes públicas:

*   **Redes Profesionales (LinkedIn, XING):** Extracción de perfiles mediante técnicas de *scraping* estructurado para identificar la jerarquía organizacional y los administradores de sistemas.
*   **Registros PGP / Metadatos de Documentos:** Análisis de archivos expuestos (PDF, DOCX, XLSX) indexados en buscadores para extraer usuarios del sistema, rutas internas y versiones de software mediante metadatos EXIF/OLE.

### B. Mapeo de Pasarelas de Autenticación y Single Sign-On (SSO)
Identificación de los endpoints donde la organización valida identidades de usuario (M365/Entra ID, Okta, Ping Identity, Citrix, VPNs):

*   **Enumeración de Dominios / Inquilinos (Tenant Enumeration):** Verificación de la presencia del dominio corporativo en infraestructuras Cloud (ej. Microsoft 365 / Entra ID) mediante peticiones a endpoints públicos (`GetCredentialType` o `GetUserRealm`).

### C. Enumeración de Usuarios (User Enumeration)
Diferenciación entre usuarios válidos e inválidos analizando las respuestas del servidor ante intentos de autenticación o resolución de identidad:

*   **Diferencias en Respuestas HTTP / Tiempos:** Respuestas con códigos de estado, mensajes de error o tiempos de procesamiento dispares (*Side-Channel Analysis*) según si el usuario existe o no en el directorio.

---

## 🛠️ 3. Guía de Ejecución y Explotación Operativa

### Paso 1: Extracción de Metadatos y Usuarios con `pyMeta` / `exiftool`
Descarga de documentos públicos indexados en buscadores y extracción automatizada de nombres de usuario y software:

```bash
# Descarga y análisis de metadatos de documentos corporativos (PDF, DOCX, XLSX)
pymeta -d objetivo.com -m 50 -o pymeta_out

# Inspección directa de metadatos en archivos locales para extraer creadores
exiftool -Creator -Author -LastModifiedBy *.pdf *.docx | sort -u
````

### Paso 2: Generación de Lista de Correos y Nombres con `crosslinked`

Scraping pasivo de estructuras de nombres a partir de perfiles públicos de LinkedIn:


```Bash
# Formatos comunes: {f}{last} (jdoe), {first}.{last} (john.doe), {first} (john)
crosslinked -f '{first}.{last}@objetivo.com' "NombreEmpresa" -o usuarios_linkedin.txt
```

### Paso 3: Identificación de Inquilinos y Servicios Cloud con `o365creeper`

Validación silenciosa de la existencia de direcciones de correo en Microsoft 365 / Entra ID mediante peticiones al API de validación:

  
```Bash
# Comprobación de lista de usuarios contra M365 sin generar intentos fallidos de login
python3 o365creeper.py -f usuarios_linkedin.txt -o usuarios_validos_m365.txt
```

### Paso 4: Ataque Controlado de Password Spraying con `kerbrute` / `spray365`

Ejecución de un único intento de autenticación por usuario con una clave de alta probabilidad (ej. `Verano2026!`, `Empresa2026!`) respetando los umbrales de bloqueo de cuenta (_Account Lockout Policy_):


```Bash
# Validar usuarios y password spray contra Active Directory expuesto o Kerberos/M365
kerbrute password-spray -d dominio.local --dc 192.168.1.10 usuarios_validos_m365.txt 'Verano2026!'
```

## 🛡️ 4. Detección, Telemetría y Mitigación (Blue Team)

- **Detección en Microsoft Entra ID / Okta:**
    
      
    - **Alertas de Password Spraying:** Múltiples intentos de autenticación fallidos procedentes de una misma dirección IP dirigidos a múltiples cuentas en un intervalo corto de tiempo (`Event ID 4625` en AD o `Sign-in logs` en Entra ID).
        
          
        
    - **Detección de User Enumeration:** Peticiones anómalas y repetitivas al endpoint `GetCredentialType` desde herramientas automáticas de recopilación.
        
          
        
- **Mitigación:**
    
      
    - Implementar autenticación de doble factor sin contraseñas (_Passwordless_) o MFA resistente a suplantación (FIDO2 / WebAuthn).
        
          
        
    - Desplegar políticas de _Smart Lockout_ en Entra ID / Okta para bloquear la IP del atacante sin bloquear la cuenta del usuario legítimo.
        
          
        
    - Sanitizar metadatos en documentos públicos mediante puertas de enlace de correo o canalizaciones de publicación web.
