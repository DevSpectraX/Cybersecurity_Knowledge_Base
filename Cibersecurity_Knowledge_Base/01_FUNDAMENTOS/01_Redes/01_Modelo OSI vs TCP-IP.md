---
tags: [redes, networking, osi, tcp-ip, fundamentos]
---


Para comprender cómo viajan los datos a través de una red, la industria utiliza modelos de referencia en capas. Estos modelos dividen el complejo proceso de la comunicación en pasos más pequeños y manejables. Los dos estándares fundamentales son el **Modelo OSI** (teórico y pedagógico) y el **Modelo TCP/IP** (práctico y real).

## 🏛️ 1. El Modelo OSI (Open Systems Interconnection)

Diseñado por la ISO, es un modelo de **7 capas** que sirve como marco de referencia teórico para entender el recorrido de la información.

1. **Capa 7: Aplicación:** Interfaz con el usuario y el software (HTTP, FTP, SSH). _Aquí los datos son legibles._

2. **Capa 6: Presentación:** Traduce, cifra y comprime los datos (Formatos como JSON, XML, cifrado SSL/TLS).

3. **Capa 5: Sesión:** Gestiona, mantiene y finaliza las conexiones (sesiones) entre las aplicaciones que se comunican.

4. **Capa 4: Transporte:** Controla la transferencia de datos de extremo a extremo, la segmentación y el control de errores/flujo (**TCP** y **UDP**).

5. **Capa 3: Red:** Se encarga del direccionamiento lógico y el enrutamiento a través de internet (**IP**, routers).

6. **Capa 2: Enlace de Datos:** Direccionamiento físico (direcciones **MAC**, switches) y detección de errores en el medio físico.

7. **Capa 1: Física:** Transmisión de bits puros sobre el medio físico (cables de cobre, fibra óptica, ondas de radio/Wi-Fi).


## 🛠️ 2. El Modelo TCP/IP (La Realidad de Internet)

A diferencia de OSI, el modelo TCP/IP se diseñó para la acción. Es un modelo de **4 capas** en el que se basa el funcionamiento real de internet y de cualquier red actual. Simplifica el modelo OSI agrupando las capas superiores e inferiores.

1. **Capa de Aplicación:** Agrupa las capas 5, 6 y 7 de OSI. Maneja los protocolos de alto nivel (HTTP, DNS, SMB).

2. **Capa de Transporte:** Equivalente a la Capa 4 de OSI. Define cómo viajan los datos (**TCP** orientado a conexión frente a **UDP** no orientado a conexión).

3. **Capa de Internet:** Equivalente a la Capa 3 de OSI. Utiliza el protocolo **IP** para encaminar los paquetes de una red a otra.

4. **Capa de Acceso a la Red:** Agrupa las capas 1 y 2 de OSI. Maneja el hardware físico, las tramas de datos y el direccionamiento MAC.


## 📊 Mapeo y Equivalencia Real

Para verlo de forma clara en tu documentación, así se cruzan ambos modelos:

|**Capa OSI**|**Capa TCP/IP**|**Unidad de Datos (PDU)**|**Dispositivo o Protocolo Real**|
|---|---|---|---|
|**7. Aplicación**<br><br>  <br><br>**6. Presentación**<br><br>  <br><br>**5. Sesión**|**1. Aplicación**|Datos|HTTP, SSH, DNS, software del sistema|
|**4. Transporte**|**2. Transporte**|**Segmento** (TCP) / Datagrama (UDP)|Puertos lógicos (Ej: 80, 22), Sockets|
|**3. Red**|**3. Internet**|**Paquete**|Dirección IP, Routers|
|**2. Enlace de Datos**|**4. Acceso a la Red**|**Trama** (_Frame_)|Dirección MAC, Switches, Tarjeta de Red (NIC)|
|**1. Física**|Bits|Cables, Ondas de radio, Hubs||

## 🔬 Datos Reales: ¿Cómo interactúan en tu Sistema Operativo?

Cuando abres una terminal de Linux o Windows y lanzas una petición web a un servidor local, tu sistema operativo procesa las capas de la siguiente manera:

1. **Aplicación:** Tu navegador web genera los _Datos_ (un `GET / HTTP/1.1`).

2. **Transporte:** El sistema operativo le asigna un puerto de origen aleatorio (ej: `53421`) y el puerto de destino `80`, empaquetándolo todo en un _Segmento_ TCP.

3. **Internet (Red):** Se le añade la IP de tu máquina (origen) y la IP del servidor (destino), convirtiéndose en un _Paquete_.

4. **Acceso a la Red:** Tu tarjeta de red traduce las IPs a direcciones físicas MAC de tu router, encapsulando el paquete en una _Trama_, que finalmente sale disparada en forma de _Bits_ (impulsos eléctricos o luz).


_MOC de Referencia:_ [[MOC_Redes_Fundamentos]]