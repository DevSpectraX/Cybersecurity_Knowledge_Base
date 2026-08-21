---
tags: [redes, networking, encapsulamiento, pdu, fundamentos]
---

El **encapsulamiento** es el proceso mediante el cual los datos de una aplicación viajan hacia abajo a través de las capas del modelo de red (OSI o TCP/IP). En cada capa, la información recibe una "cabecera" (_header_) y, en ocasiones, una "cola" (_trailer_) con metadatos específicos que los dispositivos intermedios (como routers y switches) necesitan para entregar el mensaje correctamente.

El proceso inverso, cuando el receptor recibe los bits y va retirando estas envolturas hasta dejar el dato original, se conoce como **desencapsulamiento**.

## 📦 Las Unidades de Datos de Protocolo (PDU)

A medida que la información se va transformando en cada capa, recibe un nombre técnico diferente conocido como **PDU** (_Protocol Data Unit_). Para entenderlo de forma real, imagínalo como el sistema de correos postal:

1. **Datos (Capa de Aplicación):** Es la carta que escribes (ej: una petición web `GET` o un mensaje de chat).

2. **Segmento / Datagrama (Capa de Transporte):** Metes la carta en un sobre donde apuntas los puertos (el buzón del remitente y del destinatario). Si el mensaje es muy grande, se fragmenta en varios segmentos.

3. **Paquete (Capa de Internet/Red):** Metes ese sobre dentro de una caja de envíos donde imprimes las direcciones lógicas: la **IP de origen** y la **IP de destino**.

4. **Trama / _Frame_ (Capa de Acceso a la Red):** Metes la caja en el camión de reparto. Aquí se le añaden las direcciones físicas (**MAC de origen** y **MAC del siguiente salto**) y un control de errores (FCS) para asegurar que el contenido no se corrompa en el cable.

5. **Bits (Capa Física):** El camión avanza por la carretera. En la red, esto es la transformación de la trama en impulsos eléctricos, luz o frecuencias de radio.
 

## 🔍 Anatomía Real de un Paquete en la Red

Cuando analizamos el tráfico real con herramientas como Wireshark, el encapsulamiento se vuelve completamente visible. Un único mensaje enviado por la red digital se desglosa en capas superpuestas, donde cada capa envuelve a la anterior:

Plaintext

```
+-----------------------------------------------------------------------------------------+
| TRAMA (Capa 2 - Acceso a la Red)                                                        |
| [ MAC Destino: vvv ] [ MAC Origen: xxx ] [ Tipo: IPv4 ]                                 |
| +-------------------------------------------------------------------------------------+ |
| | PAQUETE (Capa 3 - Internet/Red)                                                     | |
| | [ IP Destino: yyy ] [ IP Origen: zzz ] [ Protocolo: TCP ]                           | |
| | +---------------------------------------------------------------------------------+ | |
| | | SEGMENTO (Capa 4 - Transporte)                                                  | | |
| | | [ Puerto Destino: 80 ] [ Puerto Origen: 49215 ] [ Flags: SYN ]                  | | |
| | | +-----------------------------------------------------------------------------+ | | |
| | | | DATOS (Capa 7 - Aplicación)                                                 | | | |
| | | | "GET /index.html HTTP/1.1\r\nHost: ..."                                     | | | |
| | | +-----------------------------------------------------------------------------+ | | |
| | +---------------------------------------------------------------------------------+ | |
| +-------------------------------------------------------------------------------------+ |
+-----------------------------------------------------------------------------------------+
```

## ⚡ El Proceso de Desencapsulamiento

Cuando el servidor de destino recibe los impulsos eléctricos (Bits):

1. La tarjeta de red lee los bits y los transforma en una **Trama**. Comprueba si la MAC destino es la suya. Si coincide, quita la cabecera de Capa 2 y pasa el resto hacia arriba.

2. El sistema operativo recibe el **Paquete**. Comprueba si la IP destino es la suya. Si coincide, quita la cabecera de Capa 3 y mira qué protocolo de transporte se usó (TCP o UDP).

3. La pila de red procesa el **Segmento**. Mira el puerto de destino (ej: `80`), quita la cabecera de Capa 4 y envía los **Datos** puros al servicio que está escuchando en ese puerto (ej: el servidor web Apache o Nginx).


_MOC de Referencia:_ [[MOC_Redes_Fundamentos]]