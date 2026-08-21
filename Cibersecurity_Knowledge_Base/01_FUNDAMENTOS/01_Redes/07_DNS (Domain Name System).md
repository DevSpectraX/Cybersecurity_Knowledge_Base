---
tags: [redes, networking, dns, resolucion, registros, fundamentos]
---

# DNS (Domain Name System)

El **DNS** (Sistema de Nombres de Dominio) es un servicio crítico de la capa de Aplicación (utiliza principalmente el puerto **53 sobre UDP** para consultas de clientes y **TCP** para transferencias de zona). Su función principal es traducir los nombres de dominio legibles para los humanos (como `google.com` o `servidor.local`) en direcciones IP que las máquinas puedan entender (como `142.250.184.46`).

Sin el DNS, internet y las redes corporativas colapsarían, ya que tendríamos que recordar las direcciones numéricas de cada servidor. En ciberseguridad, el DNS es uno de los vectores más abusados para el robo de datos, el control de malware (C2) y el envenenamiento de caché.

## 🏛️ 1. La Jerarquía DNS y el Proceso de Resolución

El sistema DNS no centraliza toda la información en un solo servidor mundial; está organizado como un árbol jerárquico invertido para garantizar la velocidad y la redundancia:

1. **El Servidor Raíz (Root Servers `.`):** Está en la cúspide del árbol. No sabe las IPs de las webs, pero sabe a qué servidor redirigirte según la extensión del dominio. Existen 13 direcciones IP raíz principales en el mundo (replicadas por cientos de servidores reales).
    
2. **Servidores TLD (Top-Level Domain):** Gestionan las extensiones de dominio de primer nivel. Se dividen en genéricos (`.com`, `.org`, `.net`) y geográficos (`.es`, `.co`, `.uk`).
    
3. **Servidores Autoritativos (Authoritative Name Servers):** Son los servidores finales que realmente guardan el archivo de zona del dominio y tienen la última palabra sobre qué IP le corresponde a cada subdominio.
    

### 🔄 El Viaje de una Consulta (Resolución Recursiva)

Cuando escribes `www.ejemplo.com` en tu navegador, tu máquina realiza los siguientes pasos de forma invisible si el nombre no está guardado en su caché local:

1. Tu equipo le pregunta a su **DNS Resolver local** (el asignado por DHCP, por ejemplo, el de Cloudflare `1.1.1.1` o el de Google `8.8.8.8`): _"¿Qué IP tiene [www.ejemplo.com](https://www.ejemplo.com/)?"_.
    
2. El Resolver le pregunta al **Servidor Raíz**: _"¿Dónde está [www.ejemplo.com](https://www.ejemplo.com/)?"_. El Raíz responde: _"No lo sé, pero toma la IP del servidor TLD encargado de los `.com`"_.
    
3. El Resolver va al **Servidor TLD `.com`**: _"¿Qué IP tiene [www.ejemplo.com](https://www.ejemplo.com/)?"_. El TLD responde: _"No lo sé, pero toma la IP del Servidor Autoritativo de `ejemplo.com` (ej: ns1.ejemplo.com)"_.
    
4. El Resolver va al **Servidor Autoritativo**: _"¿Qué IP tiene [www.ejemplo.com](https://www.ejemplo.com/)?"_. El Autoritativo mira su base de datos y responde: _"La IP real es `192.168.10.25`"_.
    
5. El Resolver te entrega la IP a ti, tu máquina la guarda en caché y por fin la capa de Transporte inicia el _Three-Way Handshake_ hacia esa IP.
    

## 📋 2. Tipos de Registros DNS del Mundo Real

Los servidores autoritativos guardan la información estructurada en diferentes formatos técnicos llamados **Registros**. Conocerlos es indispensable para la fase de reconocimiento de cualquier auditoría:

- **`A` (Address):** Mapea un nombre de dominio de forma directa a una dirección **IPv4** (ej: `ejemplo.com` -> `192.168.1.100`).
    
- **`AAAA`:** Mapea un nombre de dominio directamente a una dirección **IPv6**.
    
- **`CNAME` (Canonical Name):** Es un alias. Apunta un nombre a otro nombre en lugar de a una IP (ej: `www.ejemplo.com` -> `ejemplo.com`).
    
- **`MX` (Mail Exchanger):** Especifica los servidores encargados de recibir los correos electrónicos de ese dominio (ej: `mail.ejemplo.com` con prioridad 10).
    
- **`TXT` (Text):** Permite almacenar cualquier texto arbitrario. Hoy en día es vital para la seguridad del correo, ya que ahí se guardan los registros de validación para evitar la suplantación de identidad (**SPF**, **DKIM**, **DMARC**).
    
- **`NS` (Name Server):** Indica cuáles son los servidores autoritativos para ese dominio.
    
- **`PTR` (Pointer):** Es el registro inverso. Hace lo contrario al registro `A`: le das una dirección IP y te devuelve el nombre de dominio asociado (Resolución Inversa).
    

## 💻 3. Comandos Reales de Enumeración DNS

En auditorías de seguridad y administración, nunca usamos el navegador para verificar el DNS; tiramos de la consola de comandos nativa.

### En Linux (Las herramientas profesionales estándar)

- **Consulta básica de registro A:**
```bash
host google.com
```
  
- **Consultar un registro específico (ej: registros de correo MX):**
```bash
dig google.com MX
```

- **Pedir toda la información disponible usando un servidor específico (ej: el de Cloudflare):**
```
dig @1.1.1.1 ejemplo.local ANY
```  

### En Windows (Comando nativo)

- **Consulta interactiva:**
```dos
nslookup
> set type=TXT
> google.com
```

## 💀 Enfoque de Ciberseguridad: Ataques y Abuso del DNS

El DNS es un protocolo antiguo y, por defecto, carece de cifrado (el tráfico viaja en texto plano), lo que abre la puerta a múltiples vectores de ataque:

### 1. Envenenamiento de Caché DNS (DNS Spoofing)

Un atacante consigue inyectar una respuesta DNS falsa en el servidor Resolver de una red local o en la caché de la víctima. Cuando el usuario pregunta por `banco.com`, el resolver envenenado le devuelve la IP del servidor controlado por el atacante, redirigiéndolo a una web de _Phishing_ idéntica sin que el usuario note cambios en la URL.

### 2. Transferencia de Zona Fallida (Zone Transfer AXFR)

La transferencia de zona (`AXFR`) es un mecanismo legítimo para replicar todos los registros entre un servidor DNS principal y uno secundario. Si está mal configurada y permite consultas de cualquiera, un atacante puede ejecutar `dig @ns1.objetivo.com objetivo.com AXFR` y **descargar de golpe el mapa completo de la infraestructura interna de la empresa** (descubriendo IPs de servidores de desarrollo, bases de datos ocultas, paneles de administración, etc.).

### 3. Exfiltración de Datos y Túneles DNS (DNS Tunneling)

Muchos Firewalls bloquean la navegación web o el SSH de las máquinas de una red protegida, pero **casi siempre permiten la salida de consultas DNS** hacia el exterior. Los atacantes aprovechan esto para codificar información confidencial dentro de los subdominios de una consulta DNS.

- El malware del atacante lanza una consulta como: `base64_de_la_contraseña.atrapado.com`.
    
- El paquete viaja libremente por los firewalls corporativos.
    
- El servidor autoritativo de `atrapado.com` (controlado por el atacante) recibe la consulta, decodifica el subdominio y roba la información sin levantar alertas.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Redes]]