---
tags:
  - seguridad
  - arquitectura
  - zero-trust
  - administracion
  - defensa
  - identidades
---
_El modelo está inspirado en **NIST SP 800-207**, pero incorpora ejemplos explicativos adicionales._


El modelo de **Confianza Cero (Zero Trust)** es un paradigma de seguridad informática que rompe por completo con el enfoque tradicional de "seguridad perimetral" (el cual asumía que cualquier usuario o dispositivo dentro de la red corporativa era de fiar por defecto).

Bajo la filosofía Zero Trust, la ubicación de la red (ya sea interna o externa) deja de ser un factor de validación. El modelo establece una premisa radical: **se debe desconfiar por defecto de cualquier solicitud de acceso, provenga de donde provenga, y se debe verificar cada conexión de forma explícita antes de conceder acceso.**

## 🏛️ 1. Los 3 Principios Fundamentales (NIST SP 800-207)

De acuerdo con el Instituto Nacional de Estándares y Tecnología (NIST), cualquier arquitectura que se autodenomine _Zero Trust_ debe regirse estrictamente por tres directrices:

### A) Verificar Explícitamente (Verify Explicitly)

No se asumen identidades ni estados de salud de los dispositivos. Cada solicitud de acceso a un recurso debe autenticarse y autorizarse dinámicamente utilizando todos los puntos de datos disponibles en tiempo real, incluyendo:

- Identidad del usuario (MFA).
    
- Ubicación e IP de la solicitud.
    
- Estado del dispositivo (¿Tiene el antivirus activo?, ¿Está parcheado?).
    
- Clasificación del servicio o dato que se intenta consultar.
    
- Anomalías en el comportamiento (ej: un usuario logueado en Madrid que intenta acceder 5 minutos después desde Tokio).
    

### B) Acceso de Menor Privilegio (Use Least Privilege Access)

El acceso se restringe mediante estrategias Just-In-Time (**JIT**) y Just-Enough-Access (**JEA**). No se dan permisos permanentes; los accesos se otorgan únicamente para la tarea específica que se va a realizar, limitando la visibilidad del resto de la red.

### C) Asumir la Brecha (Assume Breach)

Se opera bajo la mentalidad de que **el atacante ya está dentro de la red interna**. Por lo tanto:

- Se cifra todo el tráfico siempre que sea posible (tanto en tránsito como en reposo).
    
- Se divide la red en perímetros microscópicos mediante **Microsegmentación** (evitando que si comprometen una máquina, puedan moverse libremente a las adyacentes).
    
- Se monitoriza y analiza continuamente el entorno para detectar anomalías en tiempo real.
    

## 🏗️ 2. Componentes de la Arquitectura Zero Trust (ZTA)

Para que el sistema decida en milisegundos si te da acceso a una base de datos o te bloquea, la infraestructura se divide en dos planos lógicos:

- **Plano de Control:** Donde se toman las decisiones de seguridad corporativas.
    
- **Plano de Datos:** Por donde viaja el tráfico real de la empresa una vez aprobado.
    

### ⚙️ El Motor de Decisiones (Verificación de Veracidad Técnica)

Dentro del Plano de Control conviven los tres cerebros de una ZTA:

1. **Policy Engine (PE - Motor de Políticas):** Es el encargado de tomar la decisión final de otorgar, denegar o revocar el acceso a un recurso. Aplica las reglas del negocio comparando el contexto del usuario con las políticas de la empresa.
    
2. **Policy Administrator (PA - Administrador de Políticas):** Se comunica con el Motor de Políticas. Si el PE aprueba el acceso, el PA da la orden al componente del plano de datos para que abra la "llave" de la conexión. También genera los tokens de sesión.
    
3. **Policy Enforcement Point (PEP - Punto de Aplicación de Políticas):** Es el guardián físico o virtual en el Plano de Datos (ej: un agente instalado en tu laptop, un firewall de nueva generación o un proxy inverso). Intercepta tu conexión, le pregunta al PE si puedes pasar y, según la respuesta, abre o cierra el flujo de tus datos.
    

## 🔄 3. La Evolución: Seguridad Perimetral vs. Zero Trust

Es vital entender el cambio estructural en la gestión de redes para identificar vectores de ataque en ambas infraestructuras:

|**Característica**|**🏰 Seguridad Perimetral Tradicional**|**🌐 Arquitectura Zero Trust**|
|---|---|---|
|**Confianza**|Basada en la ubicación física/red (Dentro = Confiable; Fuera = Peligroso).|Nunca se confía. Se verifica de forma continua sin importar la red.|
|**Acceso a la Red**|Una vez dentro (vía VPN o cable), tienes visibilidad de gran parte de la subred (Lateralidad fácil).|Acceso directo y exclusivo a la aplicación solicitada. El resto de la red es invisible.|
|**Validación**|Se realiza una única vez en el inicio de sesión (Autenticación estática).|Validación continua durante toda la sesión (Si el dispositivo se infecta a mitad de sesión, se revoca el acceso).|
|**Tráfico Interno**|Habitualmente viaja en texto plano o con cifrado débil (protocolos internos desprotegidos).|Cifrado mutuo (mTLS) de extremo a extremo de forma obligatoria.|

## 💀 Enfoque de Hacking en Entornos Zero Trust

Para un operador de Red Team, auditar una empresa que migró a Zero Trust es radicalmente diferente a una red clásica con Active Directory tradicional:

- **Reducción significativa del Movimiento Lateral Masivo:** En una red tradicional, comprometer las credenciales de un administrador local te permitía usar `psexec` para saltar a cualquier máquina de la misma VLAN. En Zero Trust, la **microsegmentación** y el aislamiento a nivel de aplicación rompen este vector; aunque tengas las credenciales del Administrador, el PEP bloqueará la conexión si tu máquina de origen no está explícitamente autorizada para hablar con ese servidor específico.
    
- **El Nuevo Vector de Ataque (El Endpoint y la Identidad):** Como la red es hostil por defecto, los atacantes centran sus esfuerzos en comprometer el propio dispositivo del usuario legítimo (vía malware evasivo de EDR) o en ejecutar técnicas de **MFA Fatigue** (bombardear al usuario con notificaciones de doble factor hasta que acepte por error), intentando heredar el contexto verificado de un usuario de confianza.
    

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]