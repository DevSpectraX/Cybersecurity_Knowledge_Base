---
tags: [ciberseguridad, firewalls, networking, red_team, blue_team, defensiva]
---

# Firewalls Stateless vs Stateful

Los cortafuegos (*Firewalls*) son dispositivos de seguridad de red que controlan el tráfico entrante y saliente basándose en un conjunto de reglas de seguridad predefinidas. La diferencia fundamental en su arquitectura radica en si el motor de inspección evalúa los paquetes de manera aislada (**Stateless**) o si mantiene un registro del contexto y el estado de cada sesión TCP/UDP activa en memoria (**Stateful**).

---

## 📊 1. Comparativa Técnica y Mecanismos de Inspección

| Característica | Stateless Firewall (Sin Estado) | Stateful Firewall (Con Estado) |
| --- | --- | --- |
| **Capas del Modelo OSI** | Capa 3 (Red) y Capa 4 (Transporte). | Capa 3 (Red), Capa 4 (Transporte) y seguimiento de sesión. |
| **Criterio de Evaluación** | Revisa individualmente las cabeceras de cada paquete (IP Origen/Destino, Puerto Origen/Destino, Protocolo). | Evalúa si el paquete pertenece a una conexión legítima establecida previamente en su **Tabla de Estado** (*State Table*). |
| **Contexto de Conexión** | **Ninguno.** Trata cada paquete como un evento independiente, ignorando si es un inicio, respuesta o dato de sesión. | **Completo.** Rastrea el saludo de tres vías TCP (*3-Way Handshake*), números de secuencia (SEQ/ACK) y estados UDP/ICMP. |
| **Complejidad de Reglas** | **Alta.** Requiere definir dos reglas explícitas por cada flujo (una para el tráfico de ida y otra para el tráfico de vuelta). | **Baja.** Solo requiere una regla para permitir el tráfico de ida; el tráfico de respuesta legítimo se permite automáticamente. |
| **Rendimiento e Impacto** | **Extremadamente rápido.** Consumo de memoria/CPU mínimo al no almacenar datos de sesión en memoria. | **Mayor consumo de recursos.** Mantiene registros dinámicos en memoria RAM para cada sesión activa. |

---

## 🔄 2. Mantenimiento de la Tabla de Estado (*State Table*)

Un cortafuegos **Stateful** utiliza una estructura de datos en memoria dinámica donde registra los parámetros clave de cada flujo de comunicación activo:

* **Estructura del Registro:** `[IP Origen] : [Puerto Origen] <-> [IP Destino] : [Puerto Destino] | [Protocolo] | [Estado TCP]`

### Estados de Conexión Estándar (Ejemplo en TCP):

* **`NEW`**: Un paquete que solicita iniciar una nueva conexión (ej. un paquete con la bandera `TCP SYN` activa).
* **`ESTABLISHED`**: Paquetes asociados a una conexión que ya ha completado con éxito el apretón de manos de tres vías (`SYN` -> `SYN-ACK` -> `ACK`).
* **`RELATED`**: Paquetes que inician una nueva conexión pero están vinculados a una sesión existente (por ejemplo, el canal de datos pasivo en el protocolo FTP o un mensaje de error ICMP).
* **`INVALID`**: Paquetes que no se pueden identificar o que contienen una combinación anómala de banderas TCP/números de secuencia fuera de la ventana esperada. Se descartan automáticamente.

---

## 🥷 3. Perspectiva de Red Team: Vectores de Auditoría y Evasión

Auditar la seguridad de un perímetro requiere identificar si la barrera de filtrado aplica políticas *Stateless* o *Stateful*:

### 1. Bypasseo de Filtrado Stateless mediante Puertos de Origen / Banderas ACK

Si un firewall *Stateless* permite el tráfico saliente pero bloquea el entrante, el administrador habrá tenido que crear una regla estática para permitir los paquetes de respuesta del exterior (ej. permitir paquetes con la bandera `ACK` activa desde el puerto 80).

* **Técnica de Auditoría:** Un atacante puede forzar el envío de paquetes con la bandera `ACK` activa mediante Nmap (`nmap -sA -Pn 10.10.10.15`). Si el firewall es *Stateless*, el paquete cruzará la regla al asumir que es una "respuesta" a un tráfico interno. Un firewall *Stateful* bloqueará el paquete al no encontrar la sesión previa en su *State Table*.

### 2. Ataques de Desbordamiento de la Tabla de Estado (State Table Exhaustion / DoS)

Un cortafuegos *Stateful* asigna un bloque de memoria RAM para cada registro de conexión activa.

* **Técnica de Auditoría:** Inundar la red perimetral con miles de peticiones `TCP SYN` falsificadas (*SYN Flood*) desde múltiples direcciones IP. Si el cortafuegos no está protegido con mecanismos como **SYN Cookies**, la *State Table* se llena al 100%, provocando una denegación de servicio (DoS) que impide que usuarios legítimos establezcan nuevas conexiones.

*MOC de Referencia:* [[MOC_Seguridad_Perimetral_y_Firewalls]]