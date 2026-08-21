---
tags: [seguridad, criptografia, cifrado, aes, rsa, matematicas]
---

La criptografía aplicada es la herramienta matemática que permite transformar información legible (texto plano) en un flujo ininteligible de datos (texto cifrado) para proteger su confidencialidad. En el diseño de sistemas de seguridad y protocolos de red, la criptografía se divide en dos grandes familias basándose en la naturaleza y gestión de sus claves de cifrado: la **Criptografía Simétrica (Clave Secreta)** y la **Criptografía Asimétrica (Clave Pública)**.

## 🔑 1. Criptografía Simétrica (Clave Secreta)

En el modelo simétrico, **se utiliza la misma e idéntica clave matemática tanto para cifrar el mensaje como para descifrarlo**. Ambos extremos de la comunicación (emisor y receptor) deben poseer una copia exacta de esta clave secreta y mantenerla en absoluto secreto.

### ⚙️ Estándar de la Industria: AES (Advanced Encryption Standard)

El algoritmo simétrico por excelencia en el mundo actual es **AES** (sustituto del antiguo y vulnerable DES/3DES).

- Es un cifrado de bloques (_block cipher_) que procesa datos en matrices fijas de 128 bits.
    
- Se implementa en tres longitudes de clave: **AES-128**, **AES-192** y **AES-256**.
    
- **Veracidad Técnica:** Al día de hoy, no existe ningún ataque práctico de criptoanálisis que rompa AES-256. Un ataque de fuerza bruta requeriría más energía que la disponible en el universo observable para probar todas las combinaciones posibles $2^{256}$.
    

### ⚖️ Fortalezas y Debilidades:

- **🟢 Ventaja - Rendimiento Extremo:** Es computacionalmente ultra rápido y consume muy pocos recursos de CPU. Los procesadores modernos incluyen instrucciones de hardware nativas (AES-NI) que ejecutan este cifrado casi instantáneamente. Es ideal para cifrar discos duros enteros o bases de datos masivas.
    
- **🔴 Desventaja - El Problema de la Distribución de Claves:** Si quieres hablar de forma segura con un servidor en internet, ¿cómo le envías la clave secreta por primera vez sin que un atacante interceptando la red la capture? Si el canal es inseguro, transmitir la clave simétrica rompe la seguridad desde el inicio.
    

## 👥 2. Criptografía Asimétrica (Clave Pública)

Para resolver el problema de la distribución de claves, se inventó la criptografía asimétrica. Este modelo utiliza un **par de claves matemáticamente vinculadas entre sí mediante funciones unidireccionales de trampa (trapdoor functions)**:

1. **Clave Pública:** Es accesible para todo el mundo. Se distribuye libremente en internet y se utiliza para **Cifrar** la información.
    
2. **Clave Privada:** Es estrictamente secreta y solo la posee su dueño. Se utiliza para **Descifrar** la información cifrada por su clave pública correspondiente.
    

> ⚠️ **Regla Matemática Inmutable:** Lo que cifra la clave pública **solo** lo puede descifrar la clave privada gemela. Es matemáticamente imposible descifrar los datos usando la misma clave pública que los cifró.

### ⚙️ Algoritmos Estándar: RSA y ECC (Criptografía de Curva Elíptica)

- **RSA:** Se basa en la dificultad matemática de factorizar el producto de dos números primos extremadamente grandes. Para ser considerado seguro hoy en día, requiere longitudes de clave de al menos **2048 o 4096 bits**.
    
- **ECC (Criptografía de Curva Elíptica):** Es la evolución moderna. En lugar de factorización de primos, utiliza las propiedades geométricas de curvas algebraicas.
    
- **Veracidad Técnica:** ECC es infinitamente más eficiente que RSA. Una clave ECC de **256 bits** ofrece exactamente la misma seguridad matemática que una clave RSA gigante de **3072 bits**, consumiendo una fracción del almacenamiento, ancho de banda y batería en dispositivos móviles.
    

### ⚖️ Fortalezas y Debilidades:

- **🟢 Ventaja - Distribución Segura:** Elimina la necesidad de compartir una clave secreta por canales hostiles. Yo te doy mi clave pública, tú cifras el mensaje y solo mi clave privada podrá leerlo.
    
- **🔴 Desventaja - Lentitud Computacional:** Es extremadamente pesada y lenta (hasta 1000 veces más lenta que la simétrica) debido a las operaciones matemáticas complejas de exponenciación modular o geometría de curvas. Cifrar un archivo de 1 GB con RSA colapsaría la CPU de un servidor.
    

## 🤝 3. La Solución Real: Criptografía Híbrida

¿Cómo cifra internet tus comunicaciones (como cuando entras a tu banco mediante HTTPS)? Como la simétrica es rápida pero insegura de transmitir, y la asimétrica es segura de transmitir pero insoportablemente lenta, la ingeniería de seguridad creó los **Sistemas Criptográficos Híbridos** (el motor de protocolos como TLS/HTTPS, SSH y VPNs).

El proceso sigue un orden estricto de ingeniería:

1. **Fase Asimétrica (El Saludo / Handshake):** Tu navegador web inicia conexión con el banco. Utilizando criptografía asimétrica (habitualmente un intercambio de claves _Diffie-Hellman_ basado en curvas elípticas), ambos extremos acuerdan una **Clave Simétrica única y efímera** (llamada _Clave de Sesión_) sin revelar nada confidencial a mitad de camino.
    
2. **Fase Simétrica (La Transmisión):** Una vez acordada esa clave efímera, la fase asimétrica se apaga. Todo el contenido real de la navegación (tus contraseñas, transferencias, imágenes) se cifra usando **AES (criptografía simétrica)** por su velocidad. Cuando cierras el navegador, esa clave de sesión se destruye para siempre.
    

## 📊 Matriz Comparativa (Resumen para Consulta Rápida)

|**Criterio**|**🔑 Criptografía Simétrica**|**👥 Criptografía Asimétrica**|
|---|---|---|
|**Número de claves**|Una sola clave compartida.|Un par de claves (Pública + Privada).|
|**Velocidad**|Ultra rápida (procesamiento a nivel de hardware).|Lenta (operaciones matemáticas complejas).|
|**Uso Principal**|Cifrado de datos en reposo (Discos duros, bases de datos, archivos grandes).|Intercambio de claves de sesión, Firmas digitales, Autenticación.|
|**Longitudes Seguras**|128, 192 o 256 bits.|RSA: $\ge$ 2048 bits \| ECC: $$\g$ 256 bits.|

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]