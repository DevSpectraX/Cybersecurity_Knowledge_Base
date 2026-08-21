---
tags: [sistemas, windows, procesos, lsass, credenciales, memoria]
---

En el sistema operativo Windows, existen una serie de procesos nativos que se inician junto con el núcleo y son indispensables para la estabilidad, la gestión de sesiones y la seguridad del entorno. Como auditor de seguridad o analista forense, es obligatorio conocer la identidad, la ruta legítima y el comportamiento de estos procesos para detectar técnicas de suplantación, inyecciones de código o persistencia de malware.

## 🏛️ 1. El Ecosistema de Procesos Nativos del Sistema

Cuando Windows arranca, se genera una jerarquía estricta de procesos en Modo Usuario. Si ves alguno de estos procesos ejecutándose desde una carpeta temporal (como `AppData`) o con un nombre ligeramente alterado (ej: `svch0st.exe`), estás ante un indicador de compromiso (IoC) evidente.

### 🔹 `smss.exe` (Session Manager Subsystem)

- **Ruta Legítima:** `C:\Windows\System32\smss.exe`
    
- **Función:** Es el primer proceso en modo usuario que se crea. Se encarga de arrancar las sesiones del sistema (Sesión 0 para servicios y Sesión 1 para el usuario interactivo). Inicia los procesos `winlogon.exe` y `csrss.exe`.
    

### 🔹 `csrss.exe` (Client Server Runtime Process)

- **Ruta Legítima:** `C:\Windows\System32\csrss.exe`
    
- **Función:** Subsistema Win32 encargado de la gestión de ventanas, la creación de hilos del sistema y el control de la consola de comandos. Se ejecuta un proceso `csrss.exe` por cada sesión activa.
    

### 🔹 `wininit.exe` (Windows Initialization Process)

- **Ruta Legítima:** `C:\Windows\System32\wininit.exe`
    
- **Función:** Se ejecuta una sola vez en la Sesión 0. Inicializa componentes críticos del sistema y arranca el Administrador de Servicios (`services.exe`), el subsistema de seguridad local (`lsass.exe`) y el gestor de tablas de variables del sistema (`lsass.exe` secundario o subsistemas subordinados).
    

### 🔹 `services.exe` (Service Control Manager - SCM)

- **Ruta Legítima:** `C:\Windows\System32\services.exe`
    
- **Función:** El cerebro de los servicios en Windows. Se encarga de cargar los drivers del sistema, iniciar los servicios configurados en el arranque y gestionar sus estados (iniciado, pausado, detenido).
    

### 🔹 `svchost.exe` (Service Host)

- **Ruta Legítima:** `C:\Windows\System32\svchost.exe`
    
- **Función:** Windows implementa muchos de sus servicios nativos en formato de librerías dinámicas (`.dll`) en lugar de ejecutables independientes. Como una DLL no puede ejecutarse sola, Windows arranca instancias de `svchost.exe` para que actúen como "contenedores" y ejecuten esas librerías en memoria. Es normal ver decenas de ellos corriendo a la vez.
    

## 🔐 2. El Proceso LSASS (Local Security Authority Subsystem Service)

El proceso **`lsass.exe`** (`C:\Windows\System32\lsass.exe`) es el componente de seguridad más importante de la capa de aplicación y del modo usuario de Windows.

### ¿Cuál es su función legítima?

- Se encarga de hacer cumplir las políticas de seguridad locales.
    
- Verifica las credenciales de los usuarios cuando intentan iniciar sesión (localmente o por red).
    
- Genera los **Tokens de Acceso** que definen qué privilegios tiene tu usuario.
    
- Maneja los diferentes proveedores de autenticación del sistema (SSP), como NTLM o Kerberos.
    

## 💀 3. El Objetivo del Atacante: Extracción de Credenciales en Memoria

Para evitar pedirle la contraseña al usuario cada vez que necesita acceder a un recurso compartido en la red corporativa, **Windows almacena copias de las credenciales o de sus hashes de sesión dentro del espacio de memoria asignado al proceso LSASS**.

Esto convierte a LSASS en el principal objetivo de los atacantes en la fase de post-explotación. Si logras leer la memoria de este proceso, puedes extraer los secretos de autenticación de todos los usuarios que hayan iniciado sesión en la máquina.

### 🛠️ Mecanismo de Volcado (Dumping) de LSASS

Herramientas como **Mimikatz** revolucionaron el hacking al automatizar la lectura de la memoria de LSASS. Sin embargo, en entornos corporativos modernos, Mimikatz está completamente firmado y detectado. Por ello, los operadores de Red Team utilizan herramientas nativas del propio sistema operativo para realizar un volcado silencioso de la memoria a un archivo físico (`.dmp`) y procesarlo de forma offline en su máquina de ataque.

#### Método 1: Sysinternals ProcDump (Herramienta legítima de Microsoft)

DOS

```
procdump.exe -ma lsass.exe lsass.dmp
```

#### Método 2: A través de PowerShell usando APIs nativas (comportamiento muy vigilado)

Un atacante puede interactuar con la API `MiniDumpWriteDump` cargada desde `comsvcs.dll` para forzar al sistema a escribir la memoria de LSASS en un archivo sin levantar las alarmas tradicionales:

```Powershell
rundll32.exe C:\windows\System32\comsvcs.dll, MiniDump 724 C:\Windows\Tasks\lsass.dmp full
```

_(Donde `724` es el Process ID [PID] real del proceso `lsass.exe` en ese momento)._

Una vez obtenido el archivo `lsass.dmp`, el atacante lo descarga a su propia máquina Linux y extrae las contraseñas en texto plano o los hashes NTLM usando Mimikatz o Pypykatz, evitando activar las defensas locales del servidor de la víctima.

## 🛡️ 4. Mitigaciones Modernas: Protegiendo LSASS

Microsoft ha introducido tecnologías avanzadas para contrarrestar el robo de credenciales desde la memoria de este proceso:

1. **PPL (Protected Process Light):** Al configurar LSASS como un proceso protegido (PPL), el sistema operativo impide que incluso un usuario con privilegios de Administrador local o `SYSTEM` pueda abrir un _Handle_ con permisos de lectura (`PROCESS_VM_READ`) hacia LSASS. Bloquea las herramientas estándar de volcado.
    
2. **Credential Guard (Aislamiento por Virtualización - VBS):** Es la protección más drástica y efectiva. Utiliza las extensiones de virtualización de la CPU para crear un contenedor hiperseguro aislado del resto del sistema operativo. La parte crítica de LSASS (donde residen los secretos y las claves) se desplaza a este contenedor virtual. Si un atacante compromete el núcleo de Windows (Anillo 0) e intenta volcar la memoria de LSASS, **solo obtendrá una estructura vacía**, ya que no tiene acceso físico al espacio virtualizado gestionado por el hipervisor.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Sistemas]]