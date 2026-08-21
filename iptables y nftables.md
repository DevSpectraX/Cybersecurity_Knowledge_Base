---
tags: [ciberseguridad, iptables, nftables, netfilter, linux, firewalls, red_team, blue_team]
---
# iptables y nftables

En sistemas operativos Linux, el filtrado de paquetes en el nivel de red (Capa 3 y Capa 4) es procesado directamente por el subsistema **Netfilter** dentro del kernel. Tanto **`iptables`** (el estándar histórico) como **`nftables`** (el subsistema moderno que lo reemplaza) son utilidades de espacio de usuario (_userspace_) diseñadas para interactuar con Netfilter y definir reglas de inspección, redirección, NAT y modificación de tráfico.

  

## 🏗️ 1. Arquitectura de Netfilter: Tablas y Cadenas

Netfilter organiza la lógica de procesamiento de paquetes en **Tablas** (que definen el tipo de operación) y **Cadenas** (que determinan el punto del flujo de red donde se evalúa el paquete).

  

Plaintext

```
                     ┌──────────────────┐
                     │ Paquete Entrante │
                     └────────┬─────────┘
                              │
                        [ PREROUTING ]
                              │
                      ¿Ruta para el Host?
                     /                 \
               (Sí) /                   \ (No)
                   ▼                     ▼
             [ INPUT ]             [ FORWARD ]
                   │                     │
            ┌──────┴──────┐              │
            │ Aplicación  │              │
            │   Local     │              │
            └──────┬──────┘              │
                   │                     │
             [ OUTPUT ]                  │
                   │                     │
                   └──────────┬──────────┘
                              │
                       [ POSTROUTING ]
                              │
                     ┌────────▼────────┐
                     │ Paquete Saliente│
                     └─────────────────┘
```

### Tablas Principales

- **`filter`**: Tabla por defecto para decisiones de filtrado puro (permitir/bloquear tráfico).
    
      
    
- **`nat`**: Traducción de Direcciones de Red (SNAT, DNAT, Port Forwarding).
    
      
    
- **`mangle`**: Modificación especializada de cabeceras de paquetes (TOS, TTL, marcas de calidad de servicio QoS).
    
      
    
- **`raw`**: Configuración de exenciones para el seguimiento de estado (_Connection Tracking / conntrack_).
    
      
    

## 🔄 2. Comparativa Técnica: `iptables` vs `nftables`

|**Característica**|**iptables**|**nftables**|
|---|---|---|
|**Arquitectura Interna**|Múltiples herramientas monolíticas independientes (`iptables`, `ip6tables`, `arptables`, `ebtables`).|Marco de trabajo único y unificado para IPv4, IPv6, ARP y Ethernet Bridging.|
|**Procesamiento de Reglas**|Evaluación secuencial rígida en espacio de usuario.|Utiliza una máquina virtual ligera (bytecode) que ejecuta las reglas directamente en el kernel.|
|**Sintaxis y Estructuras**|Sintaxis basada en flags (`-A`, `-p`, `-m`). Requiere herramientas externas como `ipset` para listas masivas.|Sintaxis estructurada similar a JSON/código. Soporta colecciones, diccionarios y mapas nativos en memoria.|
|**Rendimiento**|Se degrada a medida que aumenta el número de reglas en la cadena.|Alto rendimiento constante gracias a búsquedas en mapas y tablas Hash optimizadas.|
|**Atomicidad**|Reemplaza cadenas enteras; la actualización masiva de reglas puede causar inconsistencias temporales.|Permite transacciones atómicas completas (o se aplican todas las reglas o no se aplica ninguna).|

## 💻 3. Sintaxis y Configuración Práctica

### 1. Comandos Equivalentes en `iptables` y `nftables`

#### Permitir acceso SSH (Puerto 22) e ICMP (Ping)


```Bash
# ----- iptables -----
iptables -A INPUT -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -p icmp -j ACCEPT

# ----- nftables -----
nft add rule inet filter input tcp dport 22 ct state new,established accept
nft add rule inet filter input icmp type echo-request accept
```

#### Configurar Redirección de Puertos (Port Forwarding / DNAT)


```Bash
# ----- iptables -----
iptables -t nat -A PREROUTING -p tcp --dport 8080 -j DNAT --to-destination 192.168.1.50:80

# ----- nftables -----
nft add rule ip nat prerouting tcp dport 8080 dnat to 192.168.1.50:80
```

### 2. Uso de Estructuras Avanzadas en `nftables` (Sets y Diccionarios)

Permite bloquear múltiples puertos y direcciones IP en una sola regla de alta velocidad:

  

```Bash
# Creación de una tabla y cadena personalizada en nftables
nft add table inet mi_firewall
nft add chain inet mi_firewall input { type filter hook input priority 0 \; policy drop \; }

# Permitir tráfico de bucle local (loopback) y conexiones establecidas
nft add rule inet mi_firewall input iifname "lo" accept
nft add rule inet mi_firewall input ct state established,related accept

# Permitir múltiples puertos en un solo Set dinámico
nft add rule inet mi_firewall input tcp dport { 80, 443, 2222 } accept

# Bloquear una lista explícita de IPs mediante una colección
nft add rule inet mi_firewall input ip saddr { 10.10.10.100, 192.168.1.200 } drop
```

## 🥷 4. Perspectiva de Red Team: Enumeración e Identificación de Reglas Local

Cuando un auditor o Red Teamer obtiene acceso local (_Post-Explotación_) a un sistema Linux, enumerar la configuración de Netfilter es crítico para identificar reglas de pivoteo, puertos bloqueados o restricciones de salida (_Egress Filtering_):

  

### 1. Enumeración de Reglas Activas


```Bash
# Listar reglas en iptables con contadores de paquetes (requiere root o CAP_NET_ADMIN)
iptables -L -n -v --line-numbers
iptables -t nat -L -n -v

# Listar la configuración completa de nftables
nft list ruleset
```

### 2. Detección de Bypasses en Reglas Negativas

Si el cortafuegos restringe el tráfico saliente (_Egress Filtering_) bloqueando el puerto 443 o la conexión directa a ciertas IPs, un auditor puede buscar vectores de evasión examinando las exenciones:

  

- **Interfaces de Bucle Interno (`lo`):** Muchas configuraciones aplican `ACCEPT` incondicional a todo el tráfico en la interfaz `lo`, permitiendo explotación de servicios locales bindeados a `127.0.0.1`.
    
      
    
- **Tráfico ICMP / UDP Permitido:** Si `iptables` permite paquetes ICMP sin restricción de estado, es posible establecer túneles de exfiltración de datos mediante herramientas como `ptunnel-ng`.
    
      
    
- **Cadenas Personalizadas Olvidadas:** Buscar reglas creadas por software de contenedores (Docker, Kubernetes) que modifican la tabla `nat` de `iptables` y suelen abrir puertos directamente en la interfaz pública omitiendo la cadena `INPUT`.
    
      
    

_MOC de Referencia:_ [[MOC_Seguridad_Perimetral_y_Firewalls]]