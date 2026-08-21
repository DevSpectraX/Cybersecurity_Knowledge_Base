---
tags: [ciberseguridad, ids, ips, defensiva, blue_team, red_team, fundamentos]
---

# IDS vs IPS (Intrusion Detection System vs Intrusion Prevention System)

Los sistemas **IDS** (Sistema de Detección de Intrusiones) e **IPS** (Sistema de Prevención de Intrusiones) son la piedra angular de la seguridad defensiva en red (*Blue Team*). Su función principal es inspeccionar el flujo de tráfico en tiempo real mediante el análisis de paquetes para identificar firmas maliciosas, comportamientos anómalos o intentos directos de explotación de vulnerabilidades.

Aunque comparten mecanismos de inspección similares, la diferencia fundamental entre ambos radica en su **posición arquitectónica en la red** y en su **capacidad de respuesta automática** ante una amenaza detectada.

## ⚔️ 1. Modos de Despliegue y Funcionamiento

La diferencia técnica crucial determina si el sistema actúa como un observador pasivo o como una barrera activa en el camino del tráfico.

### 1. **IDS** (Intrusion Detection System - Modo Pasivo / Promiscuo)

El IDS se despliega de forma **fuera de línea (*Out-of-band*)**. No se interpone directamente en el canal por el que pasan los datos, sino que recibe una copia completa del tráfico de red a través de un puerto **SPAN / Mirror** en un switch o mediante un dispositivo físico **TAP**.

* **Acción:** Si el motor de inspección detecta un paquete malicioso, genera una **alerta** (enviando un evento al SIEM, un correo al equipo de SOC o registrándolo en un log).
* **Impacto:** **Cero latencia** en la navegación de la red. Si el IDS se colapsa o cae, la red sigue funcionando con normalidad.
* **Limitación:** El paquete malicioso **sí llega a su objetivo** antes de que salte la alerta, ya que el IDS solo observa una copia del flujo.

### 2. **IPS** (Intrusion Prevention System - Modo Activo / Inline)

El IPS se despliega **en línea (*Inline*)**, funcionando como un puente físico o lógico dentro del flujo de la red (similar a un Firewall). Todo paquete debe entrar por una interfaz del IPS y salir por otra para llegar a su destino.

* **Acción:** Si el motor detecta un patrón de ataque, aplica una acción de mitigación inmediata como **`DROP`** (destruir el paquete) o **`REJECT`** (cerrar la conexión enviando un `TCP RST`), bloqueando la amenaza en tiempo real.
* **Impacto:** **Añade latencia** al procesamiento de tráfico, ya que debe analizar cada paquete antes de reenviarlo.
* **Riesgo:** Si el IPS falla o se colapsa, puede provocar una caída completa del enlace de red (*Single Point of Failure*) a menos que cuente con mecanismos de *Bypass* físico.

---

## 🧠 2. Métodos de Detección: Firmas vs. Anomalías

Tanto los IDS como los IPS utilizan dos metodologías principales para determinar qué tráfico debe considerarse malicioso:

### Detección Basada en Firmas (*Signature-Based*)

Compara el contenido de las cabeceras y payloads contra una base de datos de patrones conocidos (reglas escritas en texto plano, similares a las de motores como *Snort* o *Suricata*).

* **Ventaja:** Extremadamente rápido, eficiente y con un índice de falsos positivos casi nulo para ataques conocidos.
* **Desventaja:** Inútil contra ataques de día cero (*0-day*) o malware cuya firma cambie mediante técnicas de ofuscación o polimorfismo.

### Detección Basada en Comportamiento / Anomalías (*Anomaly-Based*)

Establece primero una línea base (*baseline*) del comportamiento habitual de la red (volumen de tráfico promedio, protocolos comunes, horarios de conexión) y utiliza aprendizaje o heurística para detectar desviaciones.

* **Ventaja:** Capaz de identificar ataques de día cero o comportamientos no catalogados (ej. exfiltración masiva de datos fuera de horario).
* **Desventaja:** Genera un número elevado de **falsos positivos** si la actividad legítima de la red varía con frecuencia.

---

## 🥷 3. Perspectiva de Red Team: Técnicas de Evasión

Durante un ejercicio de auditoría o Red Team, enfrentarse a un IPS requiere evitar que el tráfico desencadene las firmas de inspección profunda (*DPI*). Algunas técnicas clásicas de evasión incluyen:

### 1. Fragmentación de Paquetes IP

Si un IPS no reensambla los paquetes en memoria antes de analizarlos, se puede dividir un payload malicioso en fragmentos diminutos.

* **Mecanismo:** Herramientas como Nmap permiten enviar tráfico fragmentado (`nmap -f 10.10.10.15`). Los paquetes individuales de 8 bytes no contienen la cadena de texto completa del exploit, permitiendo que crucen el IPS y sea el sistema operativo objetivo el que los reensamble al recibirlos.

### 2. Cifrado y Ofuscación de Tráfico

Dado que los motores basados en firmas inspeccionan el contenido en texto plano, encapsular la comunicación mediante cifrado inutiliza la inspección de firmas estándar.

* **Mecanismo:** Canalizar herramientas de administración o *reverse shells* mediante túneles **TLS/SSL** (utilizando `stunnel`, `openssl` o cargadores cifrados) impide que un NIDS/NIPS pueda leer la carga útil a menos que cuente con capacidades de inspección SSL/TLS con certificados de la CA corporativa (*Mitm SSL/TLS Decryption*).

### 3. Ataques de Desincronización TCP (TTL Manipulation)

Un atacante envía paquetes con valores de **TTL (Time to Live)** calculados deliberadamente para que caduquen antes de llegar al host final pero pasen por el IDS, o viceversa. Esto confunde al motor de inspección sobre el estado real de la sesión TCP del objetivo.

*MOC de Referencia:* [[MOC_IDS_IPS]]