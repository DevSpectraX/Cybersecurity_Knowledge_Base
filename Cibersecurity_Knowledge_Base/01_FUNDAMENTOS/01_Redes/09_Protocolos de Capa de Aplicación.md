---
tags: [redes, networking, capas, protocolos, aplicacion]
---


La capa de aplicación es el nivel superior de los modelos OSI y TCP/IP. Es la interfaz directa entre el software que utilizamos (navegadores web, clientes de correo, terminales) y la infraestructura de red subyacente. Los datos aquí se manejan en formato legible para el usuario o la aplicación antes de ser segmentados y transmitidos.

## 🎯 Tabla de Referencia: Protocolos y Puertos Comunes

A continuación se detallan los puertos y protocolos estándar de la capa de aplicación más utilizados en infraestructuras reales, críticos tanto para la administración de sistemas como para la identificación de servicios en auditorías de seguridad.

|**Puerto**|**Protocolo**|**Capa de Transporte**|**Descripción Real y Caso de Uso**|
|---|---|---|---|
|**21**|**FTP** _(File Transfer Protocol)_|TCP|Transferencia de archivos. Envía credenciales y datos en **texto plano** (fácilmente interceptable con Wireshark).|
|**22**|**SSH** _(Secure Shell)_ / **SFTP**|TCP|Acceso remoto seguro a líneas de comandos y transferencia cifrada de archivos. Reemplazo seguro de Telnet y FTP.|
|**23**|**Telnet**|TCP|Acceso remoto heredado en **texto plano**. Inseguro, totalmente desaconsejado en producción.|
|**25**|**SMTP** _(Simple Mail Transfer Protocol)_|TCP|Envío y transferencia de correo electrónico entre servidores (texto plano por defecto).|
|**53**|**DNS** _(Domain Name System)_|UDP / TCP|Resolución de nombres de dominio a IPs. Usa UDP para consultas rápidas de clientes y TCP para transferencias de zona de servidores.|
|**67 / 68**|**DHCP** _(Dynamic Host Config.)_|UDP|Asignación dinámica de direccionamiento IP. El puerto 67 escucha en el servidor y el 68 en el cliente.|
|**80**|**HTTP** _(Hypertext Transfer Protocol)_|TCP|Transferencia de hipertexto para la navegación web tradicional (sin cifrar).|
|**110**|**POP3** _(Post Office Protocol v3)_|TCP|Descarga de correos desde el servidor al cliente local (suele vaciar el buzón del servidor).|
|**143**|**IMAP** _(Internet Message Access)_|TCP|Gestión y sincronización de correo electrónico directamente en el servidor desde múltiples dispositivos.|
|**443**|**HTTPS** _(HTTP Secure)_|TCP|Navegación web segura. Utiliza HTTP sobre una capa de cifrado **TLS/SSL**.|
|**445**|**SMB** _(Server Message Block)_|TCP|Compartición de archivos, impresoras y servicios en redes locales (muy crítico en entornos Windows y Active Directory).|

## 🔬 Análisis de Datos Reales: ¿Cómo se ve una cabecera HTTP?

Cuando tu navegador solicita una web (tráfico por el puerto 80), genera una petición de texto plano en la capa de aplicación. Una traza real capturada de una cabecera de petición (`HTTP Request`) luce exactamente así:

HTTP

```
GET /index.html HTTP/1.1
Host: www.ejemplo.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Connection: keep-alive
```

### 🔓 El peligro del texto plano vs El cifrado (TLS)

- **En el puerto 80 (HTTP):** Si un atacante realiza un análisis de tráfico en la red local (Sniffing), podrá leer el contenido exacto de ese `GET`, las cookies de sesión y cualquier dato de formularios enviados.

- **En el puerto 443 (HTTPS):** La capa de aplicación delega la seguridad en el protocolo **TLS**. Toda esa cabecera de texto se cifra antes de bajar a la capa de transporte, por lo que un interceptor solo verá bytes aleatorios ilegibles.


_MOC de Referencia:_ [[MOC_Redes_Fundamentos]]
