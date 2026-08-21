---
tags: [seguridad, incidentes, nist, resiliencia, continuidad, rto-rpo]
---


Por muy robustas que sean las defensas de una organización (MFA, Zero Trust, Defensa en Capas), el riesgo cero no existe. Cuando un ciberataque masivo o un desastre técnico logra materializarse, la supervivencia de la empresa depende de su capacidad de reacción y aguante.

Esta disciplina se divide en dos fases críticas de ingeniería y gobernanza: la **Respuesta a Incidentes (IR - Incident Response)**, que se enfoca en la contención técnica inmediata del ataque, y la **Continuidad de Negocio (BC / DR)**, que garantiza que la empresa siga operando y facturando a pesar de tener sus sistemas principales gravemente afectados.
## 🕒 1. Ciclo de Vida de Respuesta a Incidentes (NIST SP 800-61 Rev. 2)

El Instituto Nacional de Estándares y Tecnología (NIST) define un marco metodológico estructurado en 4 fases principales que sirven como marco de referencia para la gestión de incidentes.
### Fase 1: Preparación (Preparation)

Es la fase preventiva que ocurre antes del ataque. Consiste en entrenar al equipo de respuesta (CSIRT/CERT), redactar las políticas de actuación, configurar las herramientas de monitorización (SIEM, EDR) y asegurar que existen copias de seguridad aisladas y verificadas.

### Fase 2: Detección y Análisis (Detection & Analysis)

Consiste en identificar que un ataque está ocurriendo realmente. Se analizan los vectores de alerta (alertas de antivirus, picos de tráfico inusuales en el firewall, logs sospechosos). El equipo analiza los datos para determinar el alcance del incidente, la gravedad del impacto y la prioridad de la respuesta.

### Fase 3: Contención, Erradicación y Recuperación (Containment, Eradication & Recovery)

Es la fase operativa de choque:

- **Contención:** Detener el avance del ataque para mitigar el radio de daño. Puede ser _corto plazo_ (ej: aislando el equipo afectado de la red mediante controles de segmentación o desconexión lógica) o _largo plazo_ (instalar reglas de firewall temporales).
    
- **Erradicación:** Eliminar por completo los componentes del malware de la red, cerrar las cuentas comprometidas, eliminar las puertas traseras (_backdoors_) y parchear las vulnerabilidades explotadas.
    
- **Recuperación:** Restaurar los sistemas afectados mediante copias de seguridad, replicación, reconstrucción de sistemas o procedimientos de recuperación previamente definidos.
    

### Fase 4: Actividad Post-Incidente (Post-Incident Activity / Lecciones Aprendidas)

Una vez resuelta la crisis, el equipo se reúne para responder: _¿Qué falló?, ¿Cómo entró el atacante?, ¿Qué controles técnicos habrían evitado la brecha?_ Toda la información se documenta en un informe formal de "Lecciones Aprendidas" para actualizar la Fase 1 (Preparación) y endurecer las defensas de la organización frente a futuros ataques.

## 📈 2. Continuidad de Negocio (BCP) y Recuperación ante Desastres (DRP)

Cuando el incidente es tan severo que la infraestructura principal queda inoperativa (por ejemplo, un ataque de ransomware que cifra todo el centro de datos o un incendio físico), se activan los planes de resiliencia organizativa:

- **BCP (Business Continuity Plan):** Es un plan estratégico global de la empresa enfocado en las **personas y los procesos de negocio**. Define cómo sigue funcionando la empresa a nivel analógico o alternativo mientras los sistemas informáticos están caídos (ej: cómo procesar pedidos en papel o reubicar al personal en oficinas secundarias).
    
- **DRP (Disaster Recovery Plan):** Es un plan estrictamente técnico, subsidiario del BCP, que detalla los pasos de ingeniería informática necesarios para **recuperar y restaurar la infraestructura de TI** (servidores, redes, bases de datos) en un centro de datos secundario o en la nube.
    

## 📐 3. Métricas Críticas de Recuperación: RTO y RPO

Para diseñar un DRP efectivo y calcular el coste de las soluciones de backup y replicación, la ingeniería de seguridad se basa en dos métricas cuantitativas que determinan el umbral de tolerancia de la empresa ante la pérdida de datos y tiempo:

### ⏳ RTO (Recovery Time Objective - Objetivo de Tiempo de Recuperación)

- **Definición:** Es la **cantidad máxima de tiempo transcurrido** que una organización puede tolerar con sus servicios caídos antes de sufrir consecuencias catastróficas irreparables. Responde a la pregunta: _¿Cuánto tiempo podemos mantener un servicio o proceso crítico fuera de funcionamiento?_
    
- _Ejemplo:_ Si el RTO de una plataforma de comercio electrónico se fija en **2 horas**, el equipo de TI debe ser capaz de levantar toda la infraestructura web y de pagos en menos de 120 minutos tras el desastre.
    

### 💾 RPO (Recovery Point Objective - Objetivo de Punto de Recuperación)

- **Definición:** Es la **cantidad máxima de datos medidos en tiempo que la empresa se puede permitir perder** debido a un incidente. Define la antigüedad máxima permitida de los datos almacenados en el backup para que el negocio siga siendo viable. Responde a la pregunta: _¿Cuántos datos podemos permitirnos perder en caso de caída?_
    
- _Ejemplo:_ Si una base de datos bancaria tiene un RPO de **5 minutos**, significa que la empresa no puede permitirse perder más de 5 minutos de transacciones. Por ende, los ingenieros deben implementar replicaciones de bases de datos continuas y en tiempo real. Si tiene un RPO de 24 horas, entonces puede permitir estrategias de copia de seguridad menos frecuentes que un RPO de pocos minutos.

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]