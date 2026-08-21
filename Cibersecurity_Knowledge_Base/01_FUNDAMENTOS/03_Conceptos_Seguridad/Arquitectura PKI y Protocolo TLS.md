---
tags: [seguridad, criptografia, pki, tls, certificados, ssl]
---


Tener un par de claves asimétricas y saber firmar digitalmente no es suficiente para garantizar la seguridad en el internet global. Si entras a la web de tu banco y esta te envía su clave pública para iniciar una comunicación cifrada, ¿cómo sabe tu navegador que esa clave pública pertenece realmente a tu banco y no a un atacante que ha interceptado tu red Wi-Fi mediante un ataque _Man-in-the-Middle_ (MitM)?

Para resolver este problema de confianza a gran escala, la ingeniería de seguridad creó la **Arquitectura PKI (Infraestructura de Clave Pública)** y el protocolo **TLS (Transport Layer Security)**.

## 🏢 1. Arquitectura PKI (Public Key Infrastructure)

La **PKI** es el conjunto de roles, políticas, algoritmos, hardware y software necesarios para crear, gestionar, distribuir, usar, almacenar y revocar **Certificados Digitales** de forma segura. Su función principal es vincular matemáticamente una clave pública con la identidad real de una entidad (una persona, una empresa o un servidor web).

### ⚙️ Los Componentes Críticos de una PKI

Para que esta cadena de confianza funcione, intervienen varios actores regulados por estándares internacionales (como el **X.509 v3**):

1. **Certificate Authority (CA - Autoridad de Certificación):** Es el tercer actor de confianza (_Trusted Third Party_). Una CA es una entidad encargada de verificar la identidad de quien solicita un certificado y, si todo es correcto, **firmar digitalmente** ese certificado con su propia clave privada para garantizar ante el mundo que los datos son verídicos (ej: DigiCert, Let's Encrypt).
    
2. **Registration Authority (RA - Autoridad de Registro):** Actúa como el "asistente" de la CA. Se encarga de recibir las solicitudes de los usuarios, validar físicamente sus documentos de identidad (o la propiedad de su dominio web) y, una vez aprobados, pasárselos a la CA para que emita el certificado.
    
3. **Certificate Revocation List (CRL) y OCSP:** Mecanismos para comprobar si un certificado sigue siendo válido. Si a una empresa le roban su clave privada antes de la fecha de caducidad del certificado, la CA introduce ese certificado en una "lista negra" (CRL) o responde en tiempo real a través del protocolo **OCSP** (_Online Certificate Status Protocol_) para que los navegadores web lo rechacen de inmediato.
    

### ⛓️ La Cadena de Confianza (Trust Chain)

¿Por qué tu ordenador confía en una CA? Los sistemas operativos (Windows, Linux, macOS) y los navegadores web vienen de fábrica con un almacén de certificados (**Root Certificate Store**) que contiene las claves públicas de las CA más importantes del mundo, llamadas **CAs Raíz (Root CAs)**.

Para proteger las CAs Raíz (que se guardan desconectadas de internet bajo extremas medidas de seguridad física), estas emiten certificados para **CAs Intermedias**, y son estas últimas las que firman el certificado final de tu servidor web (Certificado de Entidad Final). Tu navegador valida el certificado de la web subiendo peldaño a peldaño por la cadena hasta llegar a la CA Raíz en la que confía por defecto.

## 🔒 2. El Protocolo TLS (Transport Layer Security)

**TLS** es el protocolo criptográfico estándar de la IETF diseñado para proporcionar comunicaciones seguras a través de una red de datos. Se sitúa entre la capa de Transporte (TCP) y la capa de Aplicación, y es el motor que transforma el protocolo inseguro HTTP en **HTTPS**, además de proteger SSH, FTP (FTPS) y tráfico de correo electrónico (IMAP/SMTP sobre TLS).

**HTTP + TLS = HTTPS**

Mas información sobre SSL y TLS en [[Fundamentos SSL, TLS]]

> ⚠️ **Alerta (Deprecación de SSL):** Aunque en el día a día de la industria se sigue usando el término "Certificado SSL", el protocolo **SSL (Secure Sockets Layer) está completamente obsoleto y roto**. SSL 3.0 fue deprecado en 2015 (RFC 7568) debido a vulnerabilidades estructurales severas (como el ataque _POODLE_). Hoy en día solo deben utilizarse las versiones de **TLS**, específicamente **TLS 1.2** y el estándar moderno **TLS 1.3**.

### 🔄 El Saludo TLS 1.3 (Handshake)

A diferencia del antiguo TLS 1.2 (que requería dos viajes de ida y vuelta de paquetes para establecer la conexión), el estándar moderno **TLS 1.3** reduce la latencia a un solo viaje de ida y vuelta (**1-RTT**) y prohíbe el uso de algoritmos criptográficos obsoletos.

El flujo simplificado de ingeniería ocurre de la siguiente manera:

1. **Client Hello:** El cliente (tu navegador) envía un paquete al servidor indicando qué suites criptográficas soporta (algoritmos simétricos, funciones hash) y, de forma proactiva, envía su parte de un intercambio de claves _Diffie-Hellman_.
    
2. **Server Hello & Key Exchange:** El servidor responde eligiendo la suite criptográfica más segura. Envía su parte del intercambio de claves Diffie-Hellman. **En este mismo paso, ambos extremos ya calculan la clave simétrica efímera de sesión en secreto**.
    
3. **Server Authentication:** El servidor envía su **Certificado Digital X.509** firmado por una CA y una firma digital de todo el saludo previo para demostrar que posee la clave privada correspondiente a ese certificado.
    
4. **Validación y Cifrado:** Tu navegador verifica el certificado contra su almacén de raíces locales. Si es válido, la sesión se da por aprobada. A partir de este microsegundo, el canal asimétrico se cierra y todo el tráfico de datos real (HTTP) se transmite cifrado bajo el algoritmo simétrico acordado (habitualmente **AES-GCM** o **ChaCha20**).
    

## 💀 Enfoque de Hacking: Ataques MitM y descifrado de tráfico

Para un analista de seguridad o un atacante, la PKI y TLS representan el blindaje más difícil de romper, pero existen vectores para evadirlo:

- **Ataques de Interceptación SSL/TLS (Inspección por Proxy):** Muchas empresas instalan un Firewall/Proxy corporativo en la red interna para inspeccionar el tráfico de los empleados en busca de malware. Para poder leer el tráfico HTTPS, la empresa genera su propia CA Raíz interna y la instala a la fuerza en los ordenadores de los empleados. El firewall descifra el tráfico del usuario, lo analiza y lo vuelve a cifrar antes de mandarlo a internet. Si un atacante compromete esa CA interna de la empresa, podrá descifrar de forma transparente el tráfico de todos los trabajadores de la corporación.
    
- **Certificate Pinning (Evasión en Auditorías de Apps Móviles):** Al auditar una aplicación móvil (Android/iOS) mediante herramientas como _Burp Suite_, los analistas instalan su certificado de proxy en el teléfono para interceptar el tráfico. Las aplicaciones seguras implementan _SSL/Certificate Pinning_, una técnica de programación que incluye dentro del código de la app el hash exacto del certificado del servidor real. Si la app detecta que el certificado de la red no coincide milimétricamente con el código de su ejecutable, bloquea la conexión inmediatamente, obligando al auditor a usar herramientas de inyección de código (como _Frida_) para romper el Pinning en la memoria del dispositivo.
    

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]
