---
tags: [ciberseguridad, fundamentos, criptografia, moc, indice]
---
# 🛡️ MOC: Conceptos Fundamentales de Seguridad

Este mapa conceptual centraliza los principios teóricos, pilares fundamentales de la ciberseguridad, criptografía aplicada y marcos de gestión de riesgos necesarios para diseñar, auditar y defender entornos corporativos seguros.

---

## 🏢 1. Principios de Seguridad y Control de Acceso
Los fundamentos que gobiernan el diseño de cualquier control defensivo y la gestión de identidades.

* **[[La Tríada CIA]]**: Los objetivos fundamentales de la seguridad: Confidencialidad, Integridad y Disponibilidad.
* **[[Mecanismos AAA (Autenticación, Autorización y Auditoría)]]**: El ciclo de control de identidades: Autenticación, Autorización y Auditoría (Trazabilidad).
* **[[Estrategias de Defensa y Menor Privilegio]]**: Reducción de la superficie de ataque mediante controles redundantes (Defensa en Capas) y asignación estricta de permisos.
* **[[El Modelo Zero Trust]]**: El paradigma moderno de seguridad: Verificación explícita, acceso de menor privilegio y asunción proactiva de la brecha (*Assume Breach*).

---

## 🔐 2. Criptografía y Protección de Datos
Mecanismos matemáticos para garantizar la privacidad, la validez y el no repudio de la información en tránsito y en reposo.

* **[[Criptografía Simétrica vs Asimétrica]]**: Uso de algoritmos de cifrado clave (AES, RSA, ECC) y la problemática del intercambio de claves.
* **[[Funciones Hash y Firmas Digitales]]**: Garantía de integridad matemática y no repudio mediante hashes (SHA-256, HMAC) y criptografía de clave pública.
* **[[Arquitectura PKI y Protocolo TLS]]**: La infraestructura de clave pública, emisión de certificados digitales, Autoridades de Certificación (CA) y el cifrado de comunicaciones en internet.

---

## ☣️ 3. Gestión de Riesgos, Amenazas y Resiliencia
Metodologías para cuantificar el impacto de los ataques, clasificar el software malicioso y reaccionar ante incidentes.

* **[[Análisis de Riesgo y Glosario]]**: El marco conceptual para evaluar la infraestructura: Activos, Amenazas, Vulnerabilidades, Impacto y cálculo del Riesgo.
* **[[Taxonomía del Malware y Vectores de Infección]]**: Análisis de comportamiento de virus, troyanos, ransomware, gusanos, exploits y técnicas de ingeniería social.
* **[[Modelado de Amenazas y STRIDE]]`**: Metodología de identificación de riesgos y vectores de ataque en la fase de diseño de aplicaciones e infraestructura.
* **[[Gestión de Incidentes y Continuidad de Negocio]]**: El ciclo de vida de respuesta a incidentes (NIST/ISO) y métricas de resiliencia ante desastres (RTO, RPO).
* **[[Marcos de Seguridad y Cumplimiento]]**: Estándares de la industria para auditorías y gobierno de IT: NIST CSF, ISO 27001, PCI-DSS y regulaciones de privacidad.

---
*MOCs Relacionados:* [[MOC_Fundamentos_Sistemas]] | [[MOC_Fundamentos_Redes]] | [[MOC_Metodologias]]