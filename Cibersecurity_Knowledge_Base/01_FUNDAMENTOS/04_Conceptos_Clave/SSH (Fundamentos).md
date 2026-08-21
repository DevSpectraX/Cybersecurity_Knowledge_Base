

**SSH** (siglas de **Secure Shell**, que significa _Caparazón Seguro_) es un protocolo de red que sirve para **conectarte a otra computadora a distancia de forma cifrada y muy segura**.

Imagina que tienes un servidor en la nube o una computadora en otra habitación y necesitas controlarla. En lugar de ir físicamente con un monitor y un teclado, usas SSH para abrir una terminal de esa computadora remota desde tu propia pantalla.

Lo revolucionario de SSH es que **todo el tráfico viaja cifrado**. Si un atacante intercepta los datos en mitad de la red, solo verá símbolos ilegibles, protegiendo tus contraseñas y comandos.

Para entender cómo funciona a nivel técnico y práctico, debes dominar estos componentes:

### 1. La Arquitectura Cliente-Servidor

- **El Servidor SSH:** Es la computadora remota a la que te quieres conectar. Tiene que estar encendida y corriendo un programa de fondo (un _demonio_ llamado `sshd`) que se queda escuchando de forma permanente en el **puerto 22** esperando que alguien intente entrar.
    
- **El Cliente SSH:** Es tu computadora local. Es desde donde lanzas el comando `ssh` para solicitar el acceso.
    

### 2. Los Métodos de Autenticación

Para demostrarle al servidor remoto que tienes permiso para entrar, existen dos métodos principales:

- **Por Contraseña (Tradicional):** Introduces el usuario y la contraseña de la máquina remota. Es sencillo, pero vulnerable a ataques de fuerza bruta si la contraseña es débil.
    
- **Por Llaves SSH (Criptográfico y Ultra Seguro):** Generas un par de llaves matemáticas en tu computadora: una **llave pública** (que se sube al servidor) y una **llave privada** (que se queda en tu máquina y nunca, bajo ningún concepto, se comparte). Al conectar, las llaves se reconocen entre sí y te deja pasar **sin pedirte contraseña**.



### 🔐 1. El Mecanismo del Intercambio de Llaves (Key Exchange)

- **¿Cómo se cifra la sesión?** SSH utiliza un algoritmo llamado _Diffie-Hellman_ al inicio de la conexión. Esto permite que el cliente y el servidor acuerden una **clave simétrica temporal** para cifrar todo el tráfico de la sesión, sin necesidad de enviar esa clave a través de la red.Hoy en día SSH puede utilizar varios algoritmos de intercambio de claves:
	- Diffie-Hellman clásico.
	- ECDH (Elliptic Curve Diffie-Hellman).
	- Curve25519 (muy común actualmente).
    
- **El archivo `known_hosts`:** La primera vez que te conectas a un servidor, SSH te avisa de que no conoce su huella digital criptográfica (_fingerprint_). Si aceptas, esa huella se guarda en `~/.ssh/known_hosts`. Si un atacante intentara interceptar tu conexión en el futuro suplantando al servidor (ataque _Man-in-the-Middle_), SSH detectará que la huella no coincide, bloqueará la conexión y te lanzará una alerta roja en la terminal.
    

### 📁 2. Estructura de Archivos Críticos en el Servidor

Cuando heredes o administres un servidor Linux, estos son los tres puntos teóricos que debes revisar para entender cómo está configurado su SSH:

- **`/etc/ssh/sshd_config`:** El archivo sagrado de configuración del demonio SSH. Desde aquí se cambia el puerto por defecto, se prohíbe el acceso directo al usuario `root` o se desactiva la autenticación por contraseña normal para forzar el uso de llaves.
    
- **`~/.ssh/authorized_keys`:** Archivo ubicado en el directorio _home_ de cada usuario del servidor. Contiene la lista de todas las **llaves públicas** autorizadas para iniciar sesión como ese usuario específico sin pedir contraseña.
    

### ⚠️ 3. Permisos Estrictos en Linux (El talón de Aquiles)

SSH es extremadamente paranoico con la seguridad de los archivos locales. Si los permisos de tu carpeta de configuración son demasiado abiertos, SSH **se negará a conectar** para proteger tu clave privada de otros usuarios del sistema. Los permisos teóricos obligatorios son:

- La carpeta `~/.ssh/` debe tener permisos estrictos **`700`** (`drwx------`).
    
- Tu llave privada (`id_rsa`) debe tener permisos estrictos **`600`** (`-rw-------`).


## Podemos probar esto de manera práctica

Si queremos probar ssh en nuestro ordenador y crear nosotros una clave, por defecto no podremos iniciar sesión en root directamente mediante una ssh porque tendremos desactivado (#PermitRootLogin yes), para activarlo, simplemente lo descomentamos (PermitRootLogin yes). Para ello buscamos esos valores en este archivo `nano /etc/ssh/sshd_config`

El directorio donde se encuentran las ssh: `cd .ssh`

Si queremos conectarnos a un servidor sin tener que proporcionar todo el rato una contraseña, por ejemplo si queremos crear usuarios, primero debemos crear el servicio `service ssh start` o reinicialo si ya estaba corriendo `service ssh restart` y por ultimo comprobaremos que esté activo con `service ssh status

Creamos un par de claves `ssh-keygen`
Esto nos dara `id_rsa` (Clave Privada) y también nos dara `id_rsa.pub` (Clave Pública)

A partir de aquí hay dos formas de seguir

1. Metiendo la clave publica en el servidor.
	Tenemos que meter la clave publica en el directorio ssh del servidor al que queramos conectarnos con ssh y tenemos que cambiarle el nombre `cp id_rsa.pub authorized_keys`
	Nos conectamos a la máquina `ssh root@localhost` y ya estamos conectados si tener que escribir la contraseña.

2. Añadiendo la clave publica en nuestro servidor y enviarle la privada al usuario que quieras que se conecte.
	1. Convertir el id_rsa en un fichero de identidad autorizado, `ssh-copy-id -i id_rsa root@localhost`.
	2. Enviar el fichero de identidad al usuario que queramos que se nos conecte.
	3. El usuario debe ejecutarlo `ssh -i id_rsa root@localhost` y ya estaría como root en nuestro servidor
