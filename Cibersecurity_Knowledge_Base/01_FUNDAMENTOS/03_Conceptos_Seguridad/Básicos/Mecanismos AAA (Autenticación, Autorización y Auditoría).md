---
tags: [seguridad, identidades, autenticacion, autorizacion, auditoria, aaa]
---

Si la Tríada CIA representa los objetivos ideales que queremos alcanzar en ciberseguridad, los **Mecanismos AAA** (_Authentication, Authorization, Accounting/Auditing_) constituyen el marco operativo y técnico para conseguirlos. Es el triple filtro secuencial obligatorio que atraviesa cualquier entidad (usuario, proceso, script) cuando intenta interactuar con un sistema informático o un recurso de red.

No se puede proteger la confidencialidad o la integridad de un archivo si no se sabe a ciencia cierta quién está intentando leerlo (Autenticación), si realmente tiene el permiso para hacerlo (Autorización) y si queda constancia escrita de que lo hizo (Auditoría).

## 🔑 1. Autenticación (Authentication) - ¿Quién eres?

La autenticación es el **proceso de verificar la identidad declarada por un usuario o sistema**. El objetivo es demostrar matemáticamente o mediante validación directa que tú eres realmente quien dices ser (ej: demostrar que eres el usuario `admin`).

Este paso se basa en combinar factores de autenticación agrupados en tres categorías tradicionales (y una moderna):

- **Algo que sabes:** Una contraseña, un código PIN o la respuesta a una pregunta de seguridad. Es el factor más débil debido a la reutilización de claves y el phishing.
    
- **Algo que tienes:** Un token físico generador de claves (RSA), una tarjeta inteligente (Smart Card), tu teléfono móvil para recibir un SMS o una aplicación autenticadora (TOTP como Google Authenticator).
    
- **Algo que eres:** Factores biométricos únicos como tu huella dactilar, el escaneo de retina o el reconocimiento facial.
    
- **Algo que haces o dónde estás (Factor de Contexto):** Tu ubicación geográfica basada en la IP, la hora del intento de inicio de sesión o tu patrón de tecleo.
    

> 🛡️ **Control Recomendado:** La implementación del **MFA** (_Multi-Factor Authentication_) exige que el usuario presente al menos dos factores de categorías distintas. Si un atacante roba una contraseña (algo que sabes), no podrá entrar al sistema porque carece del teléfono de la víctima (algo que tienes).

## 🛡️ 2. Autorización (Authorization) - ¿Qué tienes permitido hacer?

Una vez que el sistema ha verificado tu identidad (ya estás autenticado), la **Autorización determina qué recursos específicos puedes ver, modificar o ejecutar**, y cuáles tienes completamente prohibidos.

Estar autenticado en una red corporativa no significa que tengas acceso a las nóminas del departamento de Recursos Humanos o a las consolas de administración del Kernel.

### Modelos Comunes de Control de Acceso:

Para gestionar esta lógica de permisos, los sistemas operativos y las aplicaciones utilizan diferentes arquitecturas:

1. **RBAC (Role-Based Access Control):** Los permisos no se asignan a personas, sino a "Roles" o "Grupos". Los usuarios se meten dentro de esos grupos. Es el modelo estándar en Active Directory y Windows (ej: el grupo `Operadores de Red` tiene permisos automáticos para configurar switches).
    
2. **ABAC (Attribute-Based Access Control):** Es un modelo más avanzado y dinámico. Evalúa atributos del usuario, del recurso y del entorno. _Ejemplo:_ "El usuario `Juan` puede editar el archivo de finanzas corporativo **SOLO si** está conectado desde la IP de la oficina central **Y** en un horario de 9:00 a 18:00".
    
3. **DAC (Discretionary Access Control):** El propietario de un archivo tiene la total discreción de decidir quién tiene acceso a él. Es el modelo clásico de permisos de archivos en Linux (`chmod`) y Windows (`icacls`).
    

## 📋 3. Auditoría / Contabilidad (Accounting / Auditing) - ¿Qué hiciste?

La auditoría (también llamada _Accounting_ o contabilidad en entornos de red) es la **fase de recolección de datos y registro cronológico de todas las acciones realizadas por el usuario durante su sesión**. Mide el consumo de recursos (tiempo de conexión, datos transferidos) y, sobre todo, genera los registros de eventos de seguridad (Logs).

La auditoría responde a preguntas clave en una investigación forense: _¿Qué archivos modificó?, ¿A qué bases de datos accedió?, ¿Intentó ejecutar comandos con privilegios elevados?_

### 🚨 El Principio del No Repudio

Una auditoría correctamente configurada y securizada garantiza el **no repudio**: la imposibilidad de que un usuario niegue haber realizado una acción en el sistema. Si los logs demuestran con marcas de tiempo atómicas y firmas digitales que la cuenta `user_01` borró la base de datos de producción desde su dirección MAC habitual tras pasar el MFA, el usuario no puede negar la autoría del incidente.

## 💀 Enfoque de Hacking y Defensivo: Centralización TACACS+ y RADIUS

En redes empresariales, los administradores no configuran las cuentas AAA de forma local en cada switch, router o servidor; utilizan protocolos de centralización. Los dos más importantes de la industria son:

|**Característica**|**🛰️ RADIUS**|**⚙️ TACACS+ (Desarrollado por Cisco)**|
|---|---|---|
|**Cifrado**|Solo cifra la contraseña en el paquete de red. El resto del tráfico viaja en texto plano.|Cifra **el paquete completo** de la comunicación AAA, haciéndolo mucho más seguro contra sniffers.|
|**Protocolo de Transporte**|UDP (Puertos 1812 / 1813). Más rápido, pero menos fiable en la entrega de paquetes.|TCP (Puerto 49). Conexión orientada a control, garantizando que ningún log de auditoría se pierda.|
|**Separación de Funciones**|Combina la Autenticación y la Autorización en un solo paso.|Separa estrictamente la Autenticación, la Autorización y la Auditoría en procesos independientes.|

### 🕵️‍♂️ El Vector de Ataque del Lado Ofensivo:

Cuando un analista de seguridad simula un ataque interno (Pentest), busca servidores **RADIUS/TACACS+** mal configurados. Si el servidor de autenticación acepta protocolos de intercambio débiles (como _CHAP_ o _PAP_ antiguos en lugar de _EAP-TLS_), el atacante puede capturar los desafíos de autenticación desde la red mediante sniffing y romperlos por fuerza bruta para suplantar identidades en toda la infraestructura de la empresa.

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]

## 🚀 Siguiente Nota del Bloque

Guarda este archivo técnico como **`Mecanismos AAA.md`** dentro de tu carpeta **`03_Conceptos_Seguridad`**.

Ahora que tienes claro cómo se gestiona el acceso de identidades, el siguiente paso crítico en tu mapa de ruta conceptual es analizar las filosofías de diseño que restringen al máximo el radio de explosión de un ataque: **`[[Estrategias de Defensa y Menor Privilegio]]`** (donde veremos la Defensa en Capas y la reducción de superficies de ataque). ¿Seguimos avanzando?