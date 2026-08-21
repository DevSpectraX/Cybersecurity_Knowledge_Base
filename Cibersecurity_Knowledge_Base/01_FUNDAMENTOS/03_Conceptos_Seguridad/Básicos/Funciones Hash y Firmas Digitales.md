---
tags: [seguridad, criptografia, hashing, integridad, firma-digital, no-repudio]
---

Si la criptografía simétrica y asimétrica se encargan de proteger la **Confidencialidad** (bloqueando la lectura de datos a ojos no autorizados), las **Funciones Hash** y las **Firmas Digitales** se diseñaron específicamente para blindar la **Integridad** y el **No Repudio**. Permiten asegurar matemáticamente que la información no ha sido manipulada por un tercero en el trayecto y verificar con certeza absoluta la identidad del emisor.

## 🔢 1. Funciones Hash (Algoritmos de Resumen)

Una **función hash** es un algoritmo matemático determinista que toma una entrada de datos de cualquier longitud (un solo carácter, un correo electrónico o un disco duro completo de 2 TB) y la transforma en una cadena de caracteres de salida de **longitud fija**. A esta salida se le conoce como _Hash_, _Checksum_ o _Resumen Criptográfico_.

### ⚙️ Propiedades Matemáticas Inmutables (NIST)

Para que un hash sea considerado seguro criptográficamente, debe cumplir bajo diseño con cuatro características estrictas:

1. **Es Determinista:** Si introduces exactamente el mismo archivo un millón de veces, la función devolverá exactamente el mismo hash el millón de veces.
    
2. **Efecto Avalancha (Unidireccionalidad):** Es una función de un solo sentido. Es matemáticamente imposible realizar ingeniería inversa para reconstruir el archivo original a partir de su hash. Además, si cambias una sola letra (o un solo bit) en un archivo de texto de 500 páginas, el hash resultante cambiará por completo de forma radical y caótica.
    
3. **Resistencia a la Preimagen:** Dado un hash de salida, es computacionalmente imposible encontrar o fabricar un mensaje de entrada que produzca ese mismo hash.
    
4. **Resistencia a Colisiones:** Es computacionalmente inviable encontrar dos entradas de datos totalmente diferentes que generen el mismo hash de salida.
    

### 🛑 Estado Actual de los Algoritmos (Alerta de Veracidad Técnica)

- **❌ MD5 y SHA-1 (Vulnerables / Deprecados):** **No deben usarse**. Investigadores matemáticos demostraron que sufren de "colisiones prácticas". Mediante potencia de cómputo, un atacante puede crear dos archivos distintos (por ejemplo, un contrato legítimo y un malware) que compartan el mismo hash, saltándose los controles de integridad.
    
- **🟢 SHA-256 y SHA-512 (Estándar Seguro):** Pertenecen a la familia SHA-2 (Secure Hash Algorithm). Producen salidas de 256 y 512 bits respectivamente. Siguen siendo completamente seguros para su uso en producción en la industria global.
    
- **🟢 SHA-3 (La Vanguardia):** Lanzado por el NIST como una alternativa interna con una arquitectura matemática completamente diferente (diseño de esponja) para tener un respaldo de seguridad en caso de que SHA-2 sufra algún criptoanálisis teórico en el futuro.
    

## ✍️ 2. Firmas Digitales

Una **Firma Digital** es un mecanismo criptográfico que asocia la identidad de una persona o equipo al contenido de un mensaje o archivo informático. No consiste simplemente en "pegar una imagen con tu firma manuscrita" en un PDF; es un proceso matemático que fusiona las **Funciones Hash** con la **Criptografía Asimétrica**.

### ⚙️ El Proceso de Generación y Verificación Técnica

El funcionamiento técnico de una firma digital sigue un flujo simétrico inverso en el que intervienen el emisor (Alice) y el receptor (Bob):

#### Fase A: El Emisor Firma el Archivo (Alice)

1. Alice escribe un documento.
    
2. El sistema pasa el documento por una función hash (ej: SHA-256) y genera un **Hash "A"**.
    
3. El sistema cifra ese Hash "A" utilizando únicamente la **Clave Privada de Alice**.
    
4. Ese hash cifrado es la **Firma Digital**, la cual se adjunta al documento original y se envía a Bob.
    

#### Fase B: El Receptor Verifica la Firma (Bob)

1. Bob recibe el documento junto con la firma digital adjunta.
    
2. El sistema de Bob descifra la firma digital utilizando la **Clave Pública de Alice**. Al hacerlo con éxito, obtiene el **Hash "A"** original. _(Esto demuestra que solo Alice pudo firmarlo, porque solo ella posee su clave privada)_.
    
3. Al mismo tiempo, el sistema de Bob vuelve a pasar el documento recibido por la misma función hash, generando un nuevo **Hash "B"**.
    
4. El sistema compara ambos hashes:
    
    - **Si Hash "A" == Hash "B":** El documento es auténtico y no ha cambiado ni un solo bit en el camino (**Integridad**).
        
    - **Si Hash "A" $\neq$ Hash "B":** La firma es inválida, lo que significa que el archivo fue modificado en tránsito o la clave utilizada no pertenece al emisor.
        

## 🏆 3. Garantías de Seguridad Conseguidas

Al combinar estas tecnologías, se logran tres propiedades indispensables para la infraestructura legal e informática:

- **Autenticidad:** Confirmación innegable de que el mensaje fue creado y firmado por el poseedor legítimo de la clave privada.
    
- **Integridad:** Certeza absoluta de que el documento no sufrió alteraciones accidentales o maliciosas desde el momento de la firma.
    
- **No Repudio:** El emisor no puede argumentar jurídicamente que no envió el archivo. Dado que la clave privada es estrictamente secreta y solo está bajo su control, la existencia de una firma válida vincula legalmente al propietario con el documento.
    

_MOC de Referencia:_ [[MOC_Conceptos_Seguridad]]