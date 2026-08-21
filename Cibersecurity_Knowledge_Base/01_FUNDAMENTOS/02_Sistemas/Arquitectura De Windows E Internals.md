---
tags: [sistemas, windows, arquitectura, internals, syscalls, fundamentos]
---

Para atacar o defender Windows de forma avanzada (Red Team / Blue Team), es obligatorio entender cómo se organiza su memoria, sus procesos base y la transición exacta entre el software de usuario y el procesador. Esto permite comprender cómo las herramientas de explotación manipulan el sistema y cómo los controles de seguridad modernos (antivirus, EDRs, Sysmon) interceptan el comportamiento malicioso.

## 🎚️ 1. División de Privilegios: User Mode vs. Kernel Mode

La arquitectura de Windows utiliza los anillos de protección del procesador (arquitectura x86/x64) para separar radicalmente el software de usuario del núcleo del sistema operativo. Esta división es la primera línea de defensa del sistema:

- **User Mode (Modo Usuario / Anillo 3):** Es el entorno restringido donde se ejecutan las aplicaciones comunes (navegadores, editores de texto, subprocesos de usuario o tu terminal). Cada proceso tiene su propio espacio de memoria virtual privado y aislado. No tienen acceso directo al hardware; si una aplicación falla o se corrompe, el error se contiene y no tumba el sistema operativo.
    
- **Kernel Mode (Modo Núcleo / Anillo 0):** Es el entorno de ejecución del código del sistema operativo (`ntoskrnl.exe`), los servicios ejecutivos y los controladores de dispositivos (_Drivers_). Tiene acceso ilimitado al hardware y a toda la memoria física del sistema. No existe el aislamiento: un solo fallo de memoria en este anillo provoca un colapso total del sistema, generando un **Pantallazo Azul (BSOD)**.
    

## 🏗️ 2. El Tránsito de una Operación: Las APIs de Windows (Win32 API)

Las aplicaciones interactúan con el sistema operativo a través de funciones legítimas expuestas en librerías dinámicas (DLLs). Los atacantes las estudian a fondo para inyectar malware en procesos legítimos mediante funciones clave como `OpenProcess`, `VirtualAlloc`, `WriteProcessMemory` o `CreateRemoteThread`.

Cuando un binario (o un malware) solicita una de estas acciones, el flujo de datos viaja milimétricamente a través de las siguientes capas:

```Plaintext
+-------------------------------------------------------------+
| MODO USUARIO (Anillo 3)                                     |
|   [ Aplicación de Usuario ]   --> Ejemplo: notepad.exe       |
|              |                                              |
|   [ Subsystem APIs (Win32) ]  --> kernel32.dll / user32.dll  |
|              |                                              |
|   [ Native API ]              --> ntdll.dll (Capa de enlace) |
+--------------|----------------------------------------------+
|============= | (Syscall / Conmutación de Modo) =============|
+--------------v----------------------------------------------+
| MODO NÚCLEO (Anillo 0)                                      |
|   [ Windows Executive ]       --> Gestión de procesos/memoria|
|   [ Kernel del S.O. ]         --> ntoskrnl.exe              |
|   [ HAL (Hardware Abstraction Layer) ]                      |
|              |                                              |
|       [ HARDWARE ]            --> CPU, RAM, Disco, NIC      |
+-------------------------------------------------------------+
```

1. **Llamada en Userland:** El ejecutable llama a la función expuesta por las DLLs del subsistema encargadas de la API de Win32 (como `kernel32.dll` para gestión de procesos, `advapi32.dll` para seguridad y registro, o `user32.dll` para interfaces gráficas).
    
2. **Traducción Nativa (`ntdll.dll`):** Las DLLs anteriores son solo un envoltorio (_wrapper_). Toman la solicitud y llaman internamente a la función no documentada equivalente dentro de **`ntdll.dll`** (la API nativa, cuyas funciones suelen empezar por `Nt` o `Zw`, por ejemplo: `NtOpenProcess`).
    
3. **La Syscall:** `ntdll.dll` carga en el registro del procesador (`EAX/RAX`) el **System Service Descriptor Number (SSDN)**, un número de índice único que identifica la función en el Kernel. Acto seguido, ejecuta la instrucción de ensamblador `syscall` (en x64) o `sysenter` (en x86). El procesador conmuta el hilo de ejecución del Anillo 3 al Anillo 0.
    

## 🫀 3. El Interior de Kernel Mode y sus Procesos Críticos

Una vez que la CPU entra en Anillo 0, la solicitud es procesada por el núcleo de Windows, compuesto por dos subcapas principales: el **Kernel Core** (encargado de tareas de bajo nivel como la sincronización de la CPU y el despacho de hilos) y el **Windows Executive**, que gestiona los recursos a través de subsistemas específicos identificados por sus prefijos en memoria:

- **Object Manager (`Ob`):** Trata todo (procesos, archivos, claves) como "Objetos" y asigna **Handles** (identificadores numéricos) a las aplicaciones de User Mode para que interactúen con ellos.
    
- **Process Manager (`Psp`/`Ps`):** Controla la creación y destrucción de hilos y procesos.
    
- **Memory Manager (`Mi`/`Mm`):** Gestiona la memoria virtual y física.
    
- **Security Reference Monitor (`Se` - SRM):** Es el componente crítico de seguridad. Compara el **Token de Seguridad** de un usuario con la lista de control de acceso del objeto que se intenta manipular, autorizando o denegando la operación directamente en el núcleo.
    

### 🔍 Estructuras de Datos en Memoria: EPROCESS y KPROCESS

El Kernel representa cada proceso mediante una estructura de datos masiva en su espacio de memoria llamada **`EPROCESS`** (_Executive Process_). Esta estructura aloja campos críticos como el `ImageFileName`, el `Process ID`, la lista de hilos y el puntero al Token de Acceso. Su primer elemento es la estructura **`KPROCESS`**, usada para la programación de hilos en la CPU.

#### 💀 Técnica Ofensiva: DKOM (Direct Kernel Object Manipulation)

Si un atacante consigue cargar un driver malicioso o explota una vulnerabilidad en Kernel Mode, puede alterar estas estructuras directamente en memoria. Todos los procesos del sistema forman una lista doblemente encadenada. Mediante DKOM, un atacante puede modificar los punteros de su proceso malicioso para "desengancharlo" de la lista. El malware se seguirá ejecutando en la CPU, pero herramientas como el Administrador de Tareas, `tasklist` o `netstat` serán totalmente incapaces de verlo.

### ⚙️ Procesos Críticos de Windows (Objetivos Habituales)

Cuando audites un sistema, estos procesos nativos e indispensables deben estar corriendo desde sus rutas legítimas:

- **`ntoskrnl.exe`:** El propio núcleo del sistema (Kernel).
    
- **`lsass.exe` (Local Security Authority Subsystem Service):** Gestiona las políticas de seguridad y las credenciales de usuario en memoria. **Es el objetivo número uno en hacking de Windows**: los atacantes intentan dumpear su memoria con herramientas como Mimikatz para extraer contraseñas y hashes.
    
- **`services.exe`:** El gestor de servicios del sistema. Muy abusado para persistencia o escalada de privilegios modificando binarios o servicios vulnerables.
    
- **`svchost.exe`:** Proceso genérico que aloja múltiples servicios del sistema a partir de librerías DLL. Es normal ver decenas de ellos corriendo simultáneamente.
    

## 🛡️ 4. Evasión de Seguridad y Protecciones de Memoria

### 🔍 EDRs y Userland Hooking

Para interceptar actividades maliciosas (como inyecciones de código), los antivirus modernos y los EDRs corporativos modifican las DLLs en la memoria del Modo Usuario (especialmente `ntdll.dll`), inyectando una instrucción de salto (`JMP`) al inicio de las funciones nativas críticas. Cuando un programa llama a `NtCreateThreadEx`, el flujo es desviado hacia el motor de análisis del antivirus antes de llegar al Kernel.

### 🥷 Evasión mediante Syscalls Directas (Direct Syscalls)

Para evadir este control, el malware avanzado escribe directamente en su propio código binario la instrucción en ensamblador de la Syscall necesaria junto con su correspondiente número SSDN. De este modo, la herramienta ofensiva salta directamente de la aplicación al Kernel Mode (Anillo 0) sin pasar por las funciones monitoreadas de `ntdll.dll`, dejando ciego al EDR en modo usuario.

### 🔒 Protecciones Nativas del Kernel

Debido al peligro de que el código malicioso alcance el Anillo 0, Windows implementa controles estrictos combinando software y hardware:

1. **KPP (Kernel Patch Protection / PatchGuard):** Supervisa periódicamente de forma aleatoria que las estructuras críticas del Kernel (como la tabla de llamadas al sistema o el propio código de `ntoskrnl.exe`) no hayan sido modificadas o parcheadas en memoria. Si detecta una alteración, provoca deliberadamente un **BSOD** inmediato para proteger al sistema.
    
2. **SMEP (Supervisor Mode Execution Prevention):** Protección a nivel de CPU que impide que el código en Kernel Mode salte a ejecutar instrucciones que residan en páginas de memoria asignadas a User Mode, bloqueando exploits que intenten ejecutar su _shellcode_ desde procesos comunes.
    
3. **SMAP (Supervisor Mode Access Prevention):** Evita que el Kernel lea o escriba datos directamente en el espacio de memoria de User Mode sin una habilitación explícita.
    

_MOC de Referencia:_ [[MOC_Fundamentos_Sistemas]]