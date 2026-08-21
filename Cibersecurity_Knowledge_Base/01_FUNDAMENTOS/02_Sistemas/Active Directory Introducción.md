---
tags: [sistemas, windows, active-directory, kerberos, dominios, fundamentos]
---
En entornos corporativos, gestionar los usuarios, las contraseñas y los permisos de forma individual en cada ordenador es inviable. **Active Directory (AD)** es el servicio de directorio centralizado creado por Microsoft para entornos de red Windows Server. Su objetivo es permitir a los administradores de sistemas e ingenieros de seguridad gestionar las identidades, los accesos y las políticas de seguridad de toda una empresa desde una única base de datos centralizada.

En ciberseguridad, Active Directory es el ecosistema donde se desarrollan la inmensa mayoría de las auditorías de red interna corporativas, los movimientos laterales y los ataques de infraestructura a gran escala.

## 🏗️ 1. Estructura Lógica de Active Directory

Active Directory organiza los recursos de la empresa utilizando componentes jerárquicos bien definidos:

### 🔹 Objetos

Son las unidades básicas de información dentro del directorio. Un objeto puede ser un **Usuario**, un **Grupo de Seguridad**, un **Equipo** (estación de trabajo o servidor) o una **Impresora**. Cada objeto tiene atributos específicos (ej: el objeto usuario tiene atributos como nombre, correo, número de teléfono y SID).

### 🔹 Unidades Organizativas (OUs)

Son contenedores lógicos utilizados para agrupar objetos dentro de un dominio. Funcionan como las carpetas de tu disco duro. Se utilizan principalmente para estructurar la empresa por departamentos (ej: `OU=Ventas`, `OU=Sistemas`) y para delegar permisos de administración sobre esas carpetas a usuarios específicos.

### 🔹 Dominios (Domains)

Es el límite administrativo y de seguridad principal de Active Directory. Un dominio agrupa a todos los objetos y OUs de una misma red (ejemplo: `empresa.local`). Todo dominio cuenta obligatoriamente con al menos un **Controlador de Dominio** (DC - _Domain Controller_), que es el servidor Windows Server que aloja la base de datos de Active Directory (`ntds.dit`) y procesa los inicios de sesión de toda la red.

### 🔹 Árboles y Bosques (Trees & Forests)

- **Árbol (Tree):** Es un conjunto de dominios que comparten un mismo espacio de nombres contiguo y una raíz común (ej: `madrid.empresa.local` y `barcelona.empresa.local` son subdominios que cuelgan del árbol `empresa.local`).
    
- **Bosque (Forest):** Es el contenedor de nivel superior que agrupa a uno o varios árboles de dominios independientes. Representa el límite de seguridad máximo de la infraestructura. Todos los dominios de un bosque comparten un mismo **Esquema** (la definición de qué campos puede tener la base de datos) y un **Catálogo Global** (un índice de búsqueda rápida para todo el bosque).
    

## 🔐 2. El Protocolo Kerberos (El Motor de la Autenticación)

Active Directory abandonó el antiguo protocolo de autenticación NTLM en favor de **Kerberos** (Puerto **88** por defecto). Kerberos es un protocolo de autenticación de red basado en "Tickets" que utiliza criptografía de clave simétrica y un tercero de confianza para validar identidades sin enviar nunca las contraseñas a través del cable.

El servicio encargado de gestionar esto dentro del Controlador de Dominio se llama **KDC** (_Key Distribution Center_), y el proceso se divide en tres pasos fundamentales:


```Plaintext
  +-------------------+        1. AS-REQ (Pido TGT)        +-------------------+
  |                   | ---------------------------------> |                   |
  |                   | <--------------------------------- |                   |
  |      CLIENTE      |   2. AS-REP (Tomo TGT)             |    KDC (ROUTER)   |
  |     (Usuario)     |                                    |  (En el Domain    |
  |                   |   3. TGS-REQ (Envío TGT + Pido ST) |    Controller)    |
  |                   | ---------------------------------> |                   |
  |                   | <--------------------------------- |                   |
  +-------------------+   4. TGS-REP (Tomo ST para Serv)   +-------------------+
            |
            | 5. AP-REQ (Te presento mi ST)
            v
  +-------------------+
  |  SERVIDOR WEB o   |
  | COMPARTIDO (CIFS) |
  +-------------------+
```

1. **Petición del Ticket de Entrada (AS-REQ):** El usuario introduce su contraseña. Su máquina toma la marca de tiempo actual, la cifra con el hash de la contraseña del usuario y la envía al KDC. Si el KDC logra descifrarla con el hash que tiene guardado en su base de datos, confirma quién eres.
    
2. **Entrega del Ticket Maestro (AS-REP):** El KDC le devuelve al cliente un **TGT** (_Ticket Granting Ticket_). Este ticket maestro está cifrado con una clave secreta que **solo el KDC conoce** (la cuenta especial `krbtgt`). El usuario guarda el TGT en su memoria local.
    
3. **Solicitud de Acceso a un Servicio (TGS-REQ / TGS-REP):** Cuando el usuario quiere entrar a una carpeta compartida en otro servidor de la red, le presenta su TGT al KDC y le dice: _"Ya demostré quién soy con este TGT, ahora dame permiso para entrar al servidor de archivos"_. El KDC valida el TGT y le devuelve un **Service Ticket (ST)** específico para ese servicio.
    
4. **Conexión Final:** El usuario le presenta el _Service Ticket_ al servidor de archivos. El servidor lo lee, comprueba que está firmado por el Controlador de Dominio y le da acceso al usuario.
    

## 💀 Enfoque de Hacking en Active Directory: Abuso de Tickets

Debido a que el control de accesos corporativo depende enteramente de la posesión de estos tickets en la memoria del sistema operativo, los atacantes de Red Team han diseñado técnicas sumamente potentes para romper la seguridad del dominio:

### 1. Kerberoasting (Robo de Service Tickets)

Es una técnica de explotación pasiva y muy difícil de detectar.

- Cualquier usuario válido del dominio (incluso uno sin privilegios) puede solicitar al KDC un _Service Ticket_ para cualquier servicio que tenga configurado un **SPN** (_Service Principal Name_), como una base de datos SQL.
    
- El KDC le entregará el ticket cifrado con el hash de la contraseña de la cuenta que ejecuta ese servicio (a menudo cuentas de servicio gestionadas por humanos con malas contraseñas).
    
- El atacante extrae ese ticket de la memoria de su máquina, lo guarda en un archivo y utiliza herramientas como **John the Ripper** o **Hashcat** de forma offline para romper el cifrado por fuerza bruta. Si la contraseña es débil, el atacante obtiene las credenciales de la cuenta de servicio en texto plano.
    

### 2. El Ataque de Ticket Dorado (Golden Ticket)

Si un atacante logra comprometer por completo el Controlador de Dominio, el objetivo final es dumpear el hash de la contraseña de la cuenta **`krbtgt`** (la cuenta maestra que cifra los TGTs).

- Con ese hash en su poder, el atacante puede fabricar de forma falsa sus propios TGTs (Tickets Dorados) desde su máquina Linux usando herramientas como Impacket.
    
- El atacante puede escribir en el ticket falso que su usuario pertenece al grupo "Administradores del Dominio", asignarle una validez de 10 años y presentarse ante cualquier máquina de la red. Como el ticket está perfectamente firmado con la clave legítima de `krbtgt`, **toda la infraestructura de la empresa lo aceptará como administrador absoluto**, logrando persistencia total en la red.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Sistemas]]