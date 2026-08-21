---
tags: [moc, teoria-avanzada, active-directory, explotacion, evasion, redes, criptografia]
---


## 🏛️ 1. Entornos Empresariales: Active Directory (AD)
*Teoría sobre el corazón de las redes Windows en grandes corporaciones.*
*   [[Arquitectura y Componentes de Active Directory]]
*   [[Protocolo Kerberos y Flujo de Autenticación]]
*   **Vectores de Ataque Críticos en AD:**
    *   [[Ataques Kerberoasting y AS-REP Roasting]]
    *   [[Abuso de Relaciones de Confianza y Domain Dominance]]
    *   [[Movimiento Lateral y Escalada mediante Pass-The-Hash / Pass-The-Ticket]]

## 🛡️ 2. Evasión de Defensas (Bypassing)
*Conceptos y metodologías para operar sin ser detectado por los sistemas de seguridad.*
*   [[Funcionamiento de Antivirus tradicionales vs EDR]]
*   [[Técnicas de Ofuscación de Payloads y Criptografía]]
*   [[AMSI Bypass y Evasión en Entornos Windows]]
*   [[Inyección de Código en Memoria (Process Injection)]]

## 💻 3. Explotación Avanzada y Corrupción de Memoria
*Teoría de bajo nivel sobre cómo interactúan los exploits con el procesador y la memoria RAM.*
*   [[Arquitectura de la Memoria: Stack y Heap]]
*   [[Mecánica de un Buffer Overflow (Desbordamiento de Búfer)]]
*   [[Registros del Procesador (EIP, ESP, EBP)]]
*   [[Mitigaciones de Memoria (ASLR, DEP, Canary Bits)]]

## 🌐 4. Ataques Avanzados a Aplicaciones Web
*Vectores de intrusión complejos que requieren entender a fondo la lógica del backend.*
*   [[Deserialización Insegura de Objetos]]
*   [[Server-Side Request Forgery avanzado (SSRF)]]
*   [[Explotación de APIs y GraphQL (Falta de control de acceso a nivel de objeto)]]
*   [[Race Conditions (Condiciones de Carrera)]]

## 🗺️ 5. Pivotaje y Enrutamiento entre Redes (Pivoting)
*La teoría de cómo un hacker se mueve entre diferentes subredes internas una vez que compromete el primer equipo expuesto a internet.*
*   [[Concepto de Pivotaje y saltos de red]]
*   [[Redireccionamiento de Puertos (Port Forwarding) y túneles SSH]]
*   [[Proxies SOCKS y el uso de Proxychains en auditorías]]

## 🐧 6. Explotación Avanzada en Entornos Linux
*Conceptos del Kernel y configuraciones del sistema complejas que permiten la escalada definitiva.*
*   [[Explotación de Tareas de Cron (Cronjobs) y Wildcards (*)]]
*   [[Abuso de Capabilities de Linux (Permisos alternativos al SUID)]]
*   [[Vulnerabilidades a nivel de Kernel (Kernel Exploits e hilos de ejecución)]]
*   [[Escapes de Contenedores (Docker / Kubernetes Breakout)]]

## 🔐 7. Criptografía Aplicada al Hacking
*La teoría matemática que te permite romper o entender la protección de los datos.*
*   [[Cifrado Simétrico vs Asimétrico (Claves públicas y privadas)]]
*   [[Algoritmos de Hashing (MD5, SHA, NTLM) y colisiones]]
*   [[Ataques de Oráculo de Relleno (Padding Oracle Attacks)]]

## 🎭 8. Post-Explotación Avanzada y Persistencia
*Qué hace un atacante real para quedarse dentro de una empresa durante meses sin levantar sospechas.*
*   [[Métodos de Persistencia en Windows (Tareas programadas, Servicios, Registro)]]
*   [[Métodos de Persistencia en Linux (SSH Keys, Backdoors en Bash)]]
*   [[Exfiltración de Datos Encubierta (Mediante túneles DNS o ICMP)]]

---

## 🎨 Leyenda de Estado de tus Notas:
*   `[[Nota]]` (En rojo/gris pálido): Enlace preparado, pero la nota aún no ha sido creada o rellenada.
*   `[[Nota]]` (En azul/verde brillante): Nota creada con éxito y contenido completado.

---
*Índices relacionados:* [[MOC_General]] | [[MOC_Metodologias]]