---
tags: [seguridad, ingenieria, desarrollo, modelado-amenazas, stride, mitigacion]
---


El **Modelado de Amenazas (Threat Modeling)** es un procedimiento de ingeniería de seguridad estructurado que consiste en identificar, cuantificar y mitigar los riesgos de seguridad potenciales de una aplicación, sistema o infraestructura durante su **fase de diseño**, mucho antes de que se escriba el código o se desplieguen los servidores.

En lugar de reaccionar ante las vulnerabilidades cuando el sistema ya está en producción (lo cual es costoso y complejo), el modelado de amenazas permite aplicar la filosofía _Security by Design_ (Seguridad desde el diseño). La metodología más extendida y estandarizada a nivel industrial para este proceso es el modelo **STRIDE**, desarrollado originalmente por ingenieros de Microsoft.

## 🏗️ 1. El Proceso de Modelado de Amenazas

Un ejercicio completo de modelado de amenazas se estructura en torno a cuatro preguntas fundamentales que los ingenieros deben responder:

1. **¿En qué estamos trabajando?** Se desglosa la arquitectura mediante un **Diagrama de Flujo de Datos (DFD)**, identificando los límites de confianza, los almacenes de datos, los procesos y los actores externos.
    
2. **¿Qué puede salir mal?** Se aplica el framework **STRIDE** sobre cada componente del diagrama para descubrir vectores de ataque potenciales.
    
3. **¿Qué vamos a hacer al respecto?** Se diseñan e implementan las contramedidas y controles técnicos para mitigar las amenazas detectadas.
    
4. **¿Hicimos un buen trabajo?** Se valida el modelo mediante pruebas de penetración (Pentesting) o auditorías de código para asegurar que las mitigaciones son efectivas.
    

## 🎯 2. El Framework STRIDE: Desglose Técnico

**STRIDE** es un acrónimo mnemotécnico donde cada letra representa una categoría de amenaza informática específica. Cada una de estas amenazas viola directamente uno de los pilares de la seguridad de la información.

A continuación se detalla la taxonomía veridical de STRIDE, su propiedad violada y su mitigación estándar de ingeniería:

### 👤 S - Spoofing (Suplantación de Identidad)

- **Definición:** Un atacante se hace pasar por una entidad legítima (un usuario, un servidor, un proceso o una dirección IP) para acceder a un sistema protegido.
    
- **Propiedad Violada:** Confidencialidad y Autenticidad.
    
- **Mitigación Estándar:** Implementación de protocolos de autenticación fuerte (MFA), uso de certificados TLS de cliente/servidor, firmas digitales y el principio de validación explícita.
    

### ✍️ T - Tampering (Manipulación de Datos)

- **Definición:** La modificación no autorizada de datos en tránsito (paquetes de red) o en reposo (archivos, registros de bases de datos, configuraciones).
    
- **Propiedad Violada:** Integridad.
    
- **Mitigación Estándar:** Uso de funciones hash criptográficas (SHA-256), listas de control de acceso (ACLs) estrictas, cifrado de datos y firmas de integridad de datos (HMAC).
    

### 🙅‍♂️ R - Repudiation (Repudio)

- **Definición:** La capacidad de un usuario o atacante de negar haber realizado una acción dañina en el sistema debido a la falta de pruebas o logs suficientes para incriminarlo.
    
- **Propiedad Violada:** Auditoría y Trazabilidad (No Repudio).
    
- **Mitigación Estándar:** Implementación de registros de eventos (Logs) centralizados y protegidos contra escritura, almacenamiento de logs en un servidor SIEM externo y el uso de firmas digitales en transacciones críticas.
    

### 🕵️‍♂️ I - Information Disclosure (Fuga de Información)

- **Definición:** La exposición accidental o el robo de datos confidenciales por parte de entidades que no deberían tener acceso a ellos (ej: mostrar un error de base de datos detallado al usuario web o interceptar tráfico de red).
    
- **Propiedad Violada:** Confidencialidad.
    
- **Mitigación Estándar:** Cifrado de extremo a extremo (TLS), cifrado de datos en reposo (AES), enmascaramiento de datos y sanitización de mensajes de error de la aplicación.
    

### 💥 D - Denial of Service (Denegación de Servicio - DoS)

- **Definición:** Agotar los recursos del sistema (CPU, memoria, ancho de banda, conexiones de bases de datos) de forma que los usuarios legítimos no puedan acceder a los servicios.
    
- **Propiedad Violada:** Disponibilidad.
    
- **Mitigación Estándar:** Implementación de balanceadores de carga, sistemas de protección anti-DDoS perimetrales, límites de tasa (_Rate Limiting_) en APIs y escalado elástico de infraestructura.
    

### 👑 E - Elevation of Privilege (Escalada de Privilegios)

- **Definición:** Un usuario con permisos limitados (ej: un usuario común de una aplicación web) explota una vulnerabilidad en el software para obtener acceso no autorizado a funciones de nivel superior (ej: permisos de Administrador).
    
- **Propiedad Violada:** Autorización.
    
- **Mitigación Estándar:** Aplicación estricta del Principio de Menor Privilegio, validación de permisos en el lado del servidor (no confiar en el navegador del usuario) e inspección de parámetros de entrada de forma exhaustiva.
    

## 📊 Matriz de Referencia Rápida para Ingeniería

| **Amenaza (STRIDE)**       | **¿Qué se ve afectado?** | **🛡️ ¿Cómo se defiende? (Mitigación)**   |
| -------------------------- | ------------------------ | ----------------------------------------- |
| **S**poofing               | Identidad / Usuario      | Autenticación robusta y certificados.     |
| **T**ampering              | Datos / Archivos         | Hashes, Firmas y Cifrado.                 |
| **R**epudiation            | Registros / Historial    | Auditoría centralizada (Logs inmutables). |
| **I**nformation Disclosure | Privacidad               | Cifrado (En tránsito y reposo) y ACLs.    |
| **D**enial of Service      | Disponibilidad           | Redundancia, Firewalls y Rate Limiting.   |
| **E**levation of Privilege | Permisos / Roles         | Control de acceso basado en roles (RBAC). |

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]