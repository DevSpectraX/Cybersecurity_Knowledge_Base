---
tags: [seguridad, fundamentos, defensa, cia, controles]
---
La **Tríada CIA** (Confidencialidad, Integridad y Disponibilidad, por sus siglas en inglés: _Confidentiality, Integrity, Availability_) es el modelo fundamental sobre el que se construye toda la seguridad de la información. No importa si estás configurando un firewall corporativo, programando una aplicación web o auditando una infraestructura crítica; cualquier control de seguridad que implementes tiene como objetivo proteger uno o más pilares de este triángulo.

Un fallo en cualquiera de estos tres componentes puede destruir la reputación de una empresa, causar pérdidas millonarias o violar regulaciones legales de privacidad.

## 🔒 1. Confidencialidad (Confidentiality)

La confidencialidad garantiza que la información **solo sea accesible por las personas, procesos o dispositivos autorizados**, previniendo la divulgación no autorizada de datos sensibles (datos médicos, tarjetas de crédito, secretos comerciales).

### 💀 Amenazas Comunes:

- Ataques de interceptación de red (_Man-in-the-Middle_ / _Sniffing_).
    
- Robo de credenciales por _Phishing_ o ingeniería social.
    
- Ingeniería inversa de malware para extraer contraseñas en texto plano.
    

### 🛡️ Controles para la Confidencialidad:

- **Cifrado de datos:** Aplicar criptografía tanto para datos en tránsito (protocolos TLS/HTTPS, VPNs) como para datos en reposo (cifrado de discos duros con BitLocker o VeraCrypt).
    
- **Control de Acceso Estricto:** Implementación de mecanismos de autenticación robustos (MFA / Doble Factor) y control de accesos basado en roles (**RBAC**).
    
- **Enmascaramiento de datos (Masking):** Ocultar partes de la información sensible (ej: mostrar solo los últimos 4 dígitos de una tarjeta de crédito en las bases de datos de atención al cliente).
    

## 📐 2. Integridad (Integrity)

La integridad asegura que la información y los sistemas **se mantengan precisos, completos y no sufran alteraciones, modificaciones o destrucciones no autorizadas** o accidentales desde su creación hasta su recepción.

### 💀 Amenazas Comunes:

- Alteración de paquetes de red en tránsito (ataques de inyección o manipulación de datos).
    
- Modificación maliciosa de archivos del sistema o código fuente por parte de un atacante (ej: cambiar la cuenta bancaria de destino en un script de transferencias).
    
- Corrupción accidental de datos debido a fallos de hardware o cortes de energía.
    

### 🛡️ Controles para la Integridad:

- **Funciones Hash (Hashing):** Generar una "huella digital matemática" única de un archivo mediante algoritmos como SHA-256. Si un solo bit del archivo cambia, el hash resultante será completamente diferente, alertando de la alteración.
    
- **Firmas Digitales:** Combinar funciones hash con criptografía asimétrica para garantizar la autenticidad del emisor y el **no repudio** (el emisor no puede negar haber enviado el mensaje).
    
- **Sistemas de Control de Versiones y Auditoría:** Herramientas como Git o registros de eventos de bases de datos que guardan un historial milimétrico de quién, cuándo y cómo modificó un dato.


## ⚡ 3. Disponibilidad (Availability)

La disponibilidad garantiza que los usuarios autorizados **tengan acceso confiable y oportuno a los datos, aplicaciones y sistemas de la empresa siempre que lo necesiten**. Un sistema seguro pero inaccesible no sirve para nada.

### 💀 Amenazas Comunes:

- Ataques de Denegación de Servicio Distribuidos (**DDoS**), que inundan los servidores con tráfico basura hasta hacerlos colapsar.
    
- Infecciones por **Ransomware**, que cifran los servidores de la empresa impidiendo que los empleados accedan a las herramientas de trabajo.

### 🛡️ Controles para la Disponibilidad:

- **Redundancia y Alta Disponibilidad:** Utilizar configuraciones en clúster (varios servidores haciendo el mismo trabajo simultáneamente) y balanceadores de carga para repartir el tráfico.
    
- **Planes de Respaldos (Backups):** Copias de seguridad periódicas almacenadas fuera de la red local (siguiendo la regla de respaldo 3-2-1: tres copias, dos soportes diferentes, una fuera de la empresa) para recuperarse rápidamente de ataques de ransomware.
    
- **Sistemas de Alimentación Ininterrumpida (SAI / UPS):** Baterías y generadores secundarios que mantienen encendidos los servidores ante un apagón eléctrico.
    

## ⚖️ El Equilibrio de la Tríada: El Dilema de la Seguridad

En el mundo real, los tres pilares de la tríada CIA están en constante conflicto. **Aumentar drásticamente la seguridad de un pilar suele perjudicar a otro.**

> 💡 **Ejemplo Práctico:** Si para asegurar la **Confidencialidad** absoluta de un servidor médico obligas a los doctores a pasar por un login de contraseña de 20 caracteres, tres factores de autenticación biométricos diferentes y descifrar manualmente el historial del paciente, habrás destruido la **Disponibilidad** del sistema en una situación de emergencia donde cada segundo cuenta.

El trabajo de un ingeniero de seguridad no es llevar los tres pilares al 100%, sino analizar el modelo de negocio de la empresa para encontrar el equilibrio perfecto entre seguridad y usabilidad.

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]