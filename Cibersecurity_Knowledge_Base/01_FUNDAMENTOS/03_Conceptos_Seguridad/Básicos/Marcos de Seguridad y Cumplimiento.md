---
tags: [seguridad, gobernanza, cumplimiento, normativas, iso27001, nist-csf, gdpr]
---


La ciberseguridad corporativa requiere una estructura de gobernanza organizada que permita alinear los controles técnicos con los objetivos de negocio y las exigencias legales y regulatorias aplicables. Para evitar que las organizaciones diseñen sus estrategias de seguridad de forma improvisada, la industria utiliza diversos **marcos de seguridad y cumplimiento** (_frameworks_).

Estos marcos proporcionan conjuntos estandarizados de directrices, mejores prácticas y controles técnicos u organizativos ampliamente probados. Su adopción permite mejorar la gestión del riesgo, aumentar la madurez de seguridad y, cuando aplica, facilitar el cumplimiento normativo y regulatorio.

---

## 🗺️ 1. Marcos de Gestión de Ciberseguridad (Gobernanza)

Son marcos generalmente de adopción voluntaria (salvo cuando una regulación, contrato o sector específico los exija) diseñados para estructurar y gestionar el programa de seguridad de una organización.

### A) ISO/IEC 27001 (Estándar Internacional)

- **Definición:** Es una norma internacional para la implantación de un **SGSI (Sistema de Gestión de la Seguridad de la Información)**. Su enfoque no es exclusivamente técnico, sino que se basa en la gestión del riesgo, la mejora continua y la implicación de la dirección mediante el ciclo PDCA (_Plan-Do-Check-Act_).
    
- **Aspectos clave:** La organización debe identificar y evaluar sus riesgos para determinar qué controles de seguridad necesita implementar. Estos controles pueden seleccionarse utilizando como referencia el **Anexo A**, que recoge un catálogo de controles relacionados con áreas como control de accesos, gestión de activos, seguridad física, desarrollo seguro o gestión de proveedores.
    
- **Certificación:** Obtener la certificación ISO 27001 requiere superar auditorías realizadas por organismos acreditados y mantener un proceso continuo de revisión y mejora.
    

---

### B) NIST CSF (Cybersecurity Framework)

- **Definición:** Desarrollado por el Instituto Nacional de Estándares y Tecnología de Estados Unidos (NIST), es uno de los marcos más utilizados para gestionar, medir y mejorar la postura de ciberseguridad de una organización.
    
- **Funciones del NIST CSF 2.0:**
    

1. **Govern (Gobernar):** Establecer la estrategia, políticas, responsabilidades y supervisión de la gestión del riesgo de ciberseguridad.
    
2. **Identify (Identificar):** Comprender los activos, procesos, dependencias y riesgos de la organización.
    
3. **Protect (Proteger):** Implementar salvaguardas para reducir la probabilidad o el impacto de incidentes.
    
4. **Detect (Detectar):** Identificar actividades anómalas e incidentes de seguridad de forma temprana.
    
5. **Respond (Responder):** Gestionar y contener los incidentes una vez detectados.
    
6. **Recover (Recuperar):** Restaurar las capacidades operativas y servicios afectados tras un incidente.
    

- **Ventaja principal:** Proporciona una estructura flexible que puede adaptarse a organizaciones de cualquier tamaño o sector.
    

---

### C) CIS Controls

- **Definición:** Los CIS Controls son un conjunto priorizado de controles de seguridad desarrollados por el Centro para la Seguridad en Internet (CIS).
    
- **Objetivo:** Ayudar a las organizaciones a implementar medidas prácticas y medibles para reducir riesgos de seguridad de forma progresiva.
    
- **Enfoque:** Mientras ISO 27001 se centra en la gestión y NIST CSF en la gobernanza y estrategia, los CIS Controls están especialmente orientados a la implementación técnica y operativa de controles defensivos.
    

---

## ⚖️ 2. Regulaciones y Estándares de Cumplimiento

A diferencia de los marcos de gobernanza, algunas normativas y estándares pueden ser de cumplimiento obligatorio dependiendo de la actividad de la organización, el sector en el que opera o la información que procesa.

---

### A) RGPD / GDPR (Reglamento General de Protección de Datos)

- **Ámbito:** Reglamento de obligado cumplimiento para organizaciones que procesen datos personales de residentes en la Unión Europea, independientemente de dónde se encuentre la empresa.
    
- **Objetivo:** Garantizar la protección de los datos personales y los derechos de privacidad de los ciudadanos.
    
- **Exigencias clave:**
    
    - Privacidad desde el diseño (_Privacy by Design_).
        
    - Minimización de datos.
        
    - Derechos de acceso, rectificación, supresión y portabilidad.
        
    - Medidas de seguridad adecuadas al riesgo.
        
    - Obligación de notificar determinadas violaciones de seguridad de datos personales a la autoridad competente cuando exista riesgo para los derechos y libertades de las personas físicas.
        
- **Plazo de notificación:** Cuando la notificación sea obligatoria, debe realizarse sin demora indebida y, en la medida de lo posible, dentro de las 72 horas siguientes a que la organización tenga conocimiento de la brecha.
    
- **Sanciones:** Las infracciones más graves pueden alcanzar hasta 20 millones de euros o el 4 % de la facturación anual global de la organización, aplicándose la cantidad más elevada.
    

---

### B) PCI DSS (Payment Card Industry Data Security Standard)

- **Ámbito:** Estándar de seguridad exigido a organizaciones que almacenan, procesan o transmiten datos de tarjetas de pago.
    
- **Aplica a:**
    
    - Comercios electrónicos.
        
    - Entidades financieras.
        
    - Procesadores de pago.
        
    - Proveedores de servicios relacionados con pagos.
        
    - Empresas que gestionen información de tarjetas.
        
- **Exigencias clave:**
    
    - Protección del entorno de datos de titulares de tarjetas (CDE).
        
    - Segmentación de redes cuando corresponda.
        
    - Cifrado de información sensible.
        
    - Gestión segura de accesos.
        
    - Monitorización y registro de actividades.
        
    - Pruebas periódicas de seguridad.
        
    - Escaneos de vulnerabilidades mediante proveedores ASV cuando sean requeridos.
        
- **Restricción importante:** El almacenamiento del código CVV tras la autorización de una transacción está prohibido.
    

---

## 📊 Resumen de Casos de Uso

|Marco / Regulación|Tipo|¿A quién aplica?|Enfoque Principal|
|---|---|---|---|
|**ISO 27001**|Estándar Internacional|Organizaciones que desean implantar y certificar un SGSI.|Gestión integral de la seguridad basada en riesgos.|
|**NIST CSF**|Framework de Ciberseguridad|Organizaciones que desean gestionar y mejorar su postura de seguridad.|Gobernanza, gestión del riesgo y operaciones de seguridad.|
|**CIS Controls**|Controles de Seguridad|Organizaciones que buscan implementar controles técnicos priorizados.|Aplicación práctica de medidas defensivas.|
|**RGPD / GDPR**|Regulación Legal|Organizaciones que tratan datos personales de residentes de la UE.|Protección de datos personales y privacidad.|
|**PCI DSS**|Estándar de la Industria|Empresas que procesan o almacenan datos de tarjetas de pago.|Protección de datos de tarjetas y transacciones financieras.|

---

## 🎯 Conclusión

Los marcos de seguridad y cumplimiento permiten transformar la seguridad de la información en un proceso estructurado y medible.

De forma simplificada:

- **ISO 27001** ayuda a gestionar la seguridad desde una perspectiva organizativa basada en riesgos.
    
- **NIST CSF** proporciona una estructura para gobernar y mejorar la ciberseguridad.
    
- **CIS Controls** ofrece controles prácticos para implementar medidas defensivas.
    
- **RGPD** protege los datos personales y la privacidad.
    
- **PCI DSS** protege la información relacionada con tarjetas de pago.
    

La combinación de estos marcos permite a las organizaciones reducir riesgos, mejorar su resiliencia y cumplir con las obligaciones regulatorias aplicables.

---

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]