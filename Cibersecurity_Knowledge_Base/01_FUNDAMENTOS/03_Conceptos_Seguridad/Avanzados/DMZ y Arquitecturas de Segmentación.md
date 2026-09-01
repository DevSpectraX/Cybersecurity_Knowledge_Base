---
tags: [ciberseguridad, dmz, segmentacion, redes, arquitectura, blue_team, red_team, bastionado]
---
# DMZ y Arquitecturas de Segmentación

Una **DMZ (Zona Desmilitarizada)** es un diseño de arquitectura de red que crea una subred aislada intermedia situada entre la red interna privada de una organización y una red no confiable externa (como Internet). Su objetivo principal es exponer servicios públicos (servidores web, correo, DNS, VPN) sin comprometer la seguridad de la red corporativa interna en caso de que uno de estos sistemas expuestos sea vulnerado.

El principio fundamental de una segmentación eficaz se basa en el **aislamiento por zonas de confianza** y la aplicación estricta de políticas de tráfico por defecto (*Default Deny*).

---

## 🏗️ 1. Arquitecturas de Despliegue de DMZ

Existen dos diseños estándar para la implementación física o lógica de una DMZ:

### 1. Arquitectura de Monocortafuegos (Single Firewall / Three-Homed DMZ)

Un único firewall dispone de al menos tres interfaces físicas o VLANs dedicadas, administrando las reglas entre las tres zonas de confianza.

```text
                  ┌──────────────┐
                  │   Internet   │
                  └──────┬───────┘
                         │
                  ┌──────▼──────┐
                  │  Firewall   │
                  │ (3 Interf.) │
                  └──┬───────┬──┘
                     │       │
       ┌─────────────┘       └─────────────┐
       ▼                                   ▼
┌──────────────┐                    ┌──────────────┐
│     DMZ      │                    │ Red Interna  │
│ (Servidores) │                    │ (LAN / User) │
└──────────────┘                    └──────────────┘

```

* **Evaluación:** Económica y fácil de administrar, pero presenta un **único punto de fallo** (*Single Point of Failure*). Si el cortafuegos es comprometido, toda la red queda expuesta.

### 2. Arquitectura de Doble Cortafuegos (Dual Firewall / Dual-Homed DMZ)

Utiliza dos cortafuegos en cadena, idealmente de **diferentes fabricantes** para evitar que una misma vulnerabilidad (*Zero-Day*) permita atravesar ambas barreras.

```text
|Internet|-->|Firewall Perimetral|-->|DMZ (Servicios)|-->|Firewall Interno|-->|Red Interna (LAN)|
```
* **Evaluación:** Máxima seguridad y cumplimiento normativo (PCI-DSS, ISO 27001). Si el servidor web en la DMZ es comprometido, el atacante aún debe superar el segundo firewall para alcanzar la LAN.

---

## 📊 2. Matriz de Control de Tráfico (Políticas de Filtrado)

Un diseño de DMZ seguro restringe el tráfico aplicando el principio de mínimo privilegio en el flujo de comunicaciones:

| Origen | Destino | Estado del Tráfico | Razón de Seguridad |
| --- | --- | --- | --- |
| **Internet** | **DMZ** | **PERMITIDO (Restringido)** | Únicamente a los puertos públicos específicos de los servicios (ej. TCP 80/443 para web, TCP 25 para correo). |
| **Internet** | **Red Interna** | **BLOQUEADO** | Prohibido el tráfico directo sin excepción. |
| **DMZ** | **Red Interna** | **BLOQUEADO (Estricto)** | La DMZ **nunca** debe iniciar conexiones hacia la LAN. Si la DMZ necesita consultar una BD interna, la conexión la debe iniciar la LAN o usarse una VLAN intermedia de BD. |
| **Red Interna** | **DMZ** | **PERMITIDO** | Acceso para administración (SSH/RDP) o consumo de servicios internos. |
| **Red Interna** | **Internet** | **PERMITIDO (Inspeccionado)** | Navegación de usuarios pasando por proxies/NGFW con inspección de contenido. |

---

## 🛡️ 3. Principios de Bastionado y Microsegmentación

Para evitar el movimiento lateral dentro de la propia DMZ en caso de compromiso de un servidor, se aplican controles de segmentación avanzada:

1. **Aislamiento Intra-DMZ (VLANs / PVLANs):** Un servidor web comprometido en la DMZ no debe poder comunicarse directamente por la red local con el servidor de correo que reside en la misma zona. Se utilizan *Private VLANs* (PVLANs) o reglas de aislamiento para bloquear el tráfico *East-West* (horizontal).
2. **Uso de Proxies Inversos y Bastiones (Jump Hosts):** La administración remota desde la LAN hacia los servidores de la DMZ debe realizarse a través de una máquina bastión intermedia con autenticación multifactor (MFA) y registro de auditoría.
3. **Bases de Datos fuera de la DMZ:** Las bases de datos que contienen información sensible jamás deben instalarse dentro de la DMZ. Se ubican en una zona de datos interna aislada, permitiendo únicamente las consultas desde la DMZ mediante puertos específicos y autenticados (ej. TCP 3306, 5432).

---

## 🥷 4. Perspectiva de Red Team: Pivotaje desde una DMZ Comprometida

Cuando un auditor o atacante consigue una shell en un servidor ubicado en la DMZ (por ejemplo, mediante una vulnerabilidad RCE en una aplicación web):

### 1. Reconocimiento de la Red Local (*Network Discovery*)

El primer objetivo es evaluar las restricciones de la red y buscar posibles brechas en las reglas de segmentación hacia la red interna:

```bash
# Identificar interfaces y subredes alcanzables
ip a / ifconfig
route -n

# Escaneo sigiloso hacia la LAN interna buscando excepciones de firewall
nmap -sT -Pn -n -p 22,80,443,445,3389 192.168.10.0/24

```

### 2. Explotación de Excepciones de Salida (*Egress Rule Abuse*)

Los administradores suelen bloquear el tráfico entrante a la DMZ pero a menudo olvidan restringir el tráfico saliente (*Egress Filtering*) iniciado desde la propia DMZ.

* **Vectores de Pivoteo:** Si el firewall permite el tráfico saliente desde la DMZ hacia cualquier destino en la LAN por ciertos puertos (ej. DNS/53, NTP/123 o SNMP/161), el auditor puede utilizar técnicas de **SOCKS Proxying** o túneles (con herramientas como `Chisel`, `Ligolo-ng` o `SSH Dynamic Port Forwarding`) para convertir el servidor web comprometido en una pasarela de salto directa hacia la red privada corporativa.

*MOC de Referencia:* [[MOC_Seguridad_Perimetral_y_Firewalls]]