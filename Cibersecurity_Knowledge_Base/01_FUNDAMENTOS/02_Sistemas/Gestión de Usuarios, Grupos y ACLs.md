---
tags: [sistemas, windows, seguridad, sids, acls, fundamentos]
---

Para auditar la seguridad de un entorno Windows o realizar una escalada de privilegios efectiva, es imprescindible comprender cómo el sistema operativo identifica a los usuarios y cómo gestiona el control de acceso a los recursos del sistema (archivos, carpetas, llaves de registro). Windows no utiliza los nombres de usuario de texto de forma interna; depende de identificadores matemáticos y listas estructuradas.

## 🆔 1. SIDs (Security Identifiers) y RIDs (Relative Identifiers)

Un **SID** es un valor estructurado de longitud variable que identifica de forma única y global a una cuenta de usuario, un grupo de seguridad o una sesión en entornos Windows.

### Estructura de un SID Real

Un SID luce exactamente así: `S-1-5-21-3623811015-3361044348-30300820-1001`

Si lo desglosamos, entenderemos su jerarquía:

- **`S`:** Identifica que la cadena es un SID.
    
- **`1`:** El nivel de revisión del formato del SID (siempre es 1).
    
- **`5`:** El identificador de la autoridad de seguridad (en este caso, NT Authority).
    
- **`21-3623811015-3361044348-30300820`:** El identificador de la sub-autoridad. Representa de forma única a la máquina local o al dominio de Active Directory.
    
- **`1001` (El RID):** El **Relative Identifier**. Es el número final que identifica al usuario o grupo específico dentro de esa máquina o dominio.
    

### 🚩 RIDs Genéricos Conocidos (Well-Known RIDs)

Existen ciertos RIDs fijos que son idénticos en todas las máquinas Windows del mundo. En ciberseguridad, buscarlos te permite identificar el rol de una cuenta sin importar el idioma del sistema operativo:

|**Cuenta / Grupo**|**SID Completo / Terminación**|**Descripción Técnica**|
|---|---|---|
|**Administrador Local**|Termina en **`-500`**|La cuenta de administración integrada (deshabilitada por defecto en sistemas modernos).|
|**Invitado (Guest)**|Termina en **`-501`**|Cuenta con privilegios mínimos para accesos temporales.|
|**SYSTEM**|**`S-1-5-18`**|El SID de la cuenta de sistema. Tiene más privilegios que el propio administrador.|
|**Grupo Administradores**|**`S-1-5-32-544`**|El alias de grupo local que otorga control total sobre la máquina.|
|**Grupo Usuarios**|**`S-1-5-32-545`**|Usuarios comunes de la máquina.|

## 👥 2. Grupos de Seguridad Locales Privilegiados

Cuando comprometes una máquina Windows, tu objetivo en la fase de reconocimiento local es verificar si el usuario que has suplantado pertenece a alguno de estos grupos de alto impacto:

- **Administradores (_Administrators_):** Tienen control ilimitado sobre todo el sistema operativo. Pueden modificar el Kernel (cargar drivers) y manipular cualquier archivo.
    
- **Operadores de Copia de Seguridad (_Backup Operators_):** Diseñado para que los técnicos respalden datos. Sus miembros pueden leer **cualquier archivo** del sistema saltándose las listas de control de acceso (ACLs), lo que permite robar colmenas del registro o datos sensibles del Administrador.
    
- **Usuarios de Escritorio Remoto (_Remote Desktop Users_):** Permite iniciar sesión de forma gráfica en la máquina a través del protocolo RDP (Puerto 3389).
    
- **Usuarios de Administración de Escritorio (_Remote Management Users_):** Permite conectarse a la máquina por consola remota usando PowerShell Remoting / WinRM (Puertos 5985/5986). Es un objetivo principal en movimientos laterales.
    

## 🔐 3. Arquitectura del Control de Acceso: ACLs, DACLs y SACLs

Windows gestiona la seguridad de los objetos (archivos, carpetas, servicios) mediante descriptores de seguridad. El componente principal de estos descriptores es la **ACL** (_Access Control List_), que se divide en dos tipos:

### A) DACL (Discretionary Access Control List)

Es la lista que decide **quién tiene permiso y quién no**. Está compuesta por múltiples **ACEs** (_Access Control Entries_). Cada ACE contiene un SID y el permiso que se le aplica (Lectura, Escritura, Control Total).

- _Regla Crítica de Windows:_ Las ACEs de **Denegación** explícita (`Deny`) siempre se procesan antes que las ACEs de **Permisión** (`Allow`). Si perteneces a un grupo que tiene permitido leer un archivo, pero a tu usuario individual se le ha configurado un `Deny`, se te bloqueará el acceso.
    

### B) SACL (System Access Control List)

No concede ni deniega accesos. Su función es la **auditoría**. Define qué acciones realizadas por qué usuarios deben generar un evento de registro (Log) en el Visor de Sucesos de Windows (ejemplo: registrar cada vez que alguien intente modificar un archivo crítico de la empresa).

## 🛠️ 4. Gestión Práctica de Permisos con `icacls`

La herramienta nativa por excelencia en la consola de comandos de Windows para visualizar y modificar las ACLs de los archivos es **`icacls`**. Es indispensable dominarla para buscar malas configuraciones de permisos que nos permitan escalar privilegios.

### 🔍 Comandos Esenciales de Auditoría:

#### 1. Ver los permisos de un archivo o directorio:

```DOS
icacls C:\Windows\System32\config
```

#### 2. Significado de las Máscaras de Permiso Comunes:

Al ejecutar el comando anterior, verás letras entre paréntesis junto a los nombres de usuario o SIDs:

- `(F)`: Control Total (_Full Control_).
    
- `(M)`: Modificación (_Modify_). Permite leer, escribir y eliminar el archivo.
    
- `(RX)`: Lectura y Ejecución (_Read and Execute_).
    
- `(R)`: Solo Lectura (_Read_).
    
- `(W)`: Solo Escritura (_Write_).
    

#### 3. Herencia de Permisos:

Los objetos suelen heredar los permisos de la carpeta contenedora superior. Verás estas marcas de herencia:

- `(I)`: Permiso heredado del contenedor padre.
    
- `(OI)`: Herencia de objeto (los archivos dentro de esta carpeta heredarán este permiso).
    
- `(CI)`: Herencia de contenedor (las subcarpetas dentro de esta carpeta heredarán este permiso).
    

### 💀 El Escenario Vulnerable: Modificación de Binarios de Servicio

Durante un ejercicio de Red Team, utilizas `icacls` para revisar los permisos de una carpeta donde se aloja un programa de terceros que corre como servicio del sistema bajo la cuenta `SYSTEM`:

```DOS
icacls "C:\Program Files\SoftwareVulnerable"
```

Salida recibida:

```Plaintext
C:\Program Files\SoftwareVulnerable BUILTIN\Usuarios:(OI)(CI)(M)
```

#### 🕵️‍♂️ Análisis del Fallo:

El grupo `BUILTIN\Usuarios` (usuarios comunes sin privilegios) tiene permisos de **Modificación `(M)`** en esa carpeta. Esto significa que un usuario de bajo nivel puede entrar, renombrar el ejecutable legítimo del servicio (ejemplo: `servicio.exe`) y sustituirlo por un binario malicioso (un ejecutable de command & control) con el mismo nombre.

Al reiniciar la máquina o el servicio, el sistema operativo ejecutará el binario del atacante con privilegios de **SYSTEM**, completando con éxito la escalada de privilegios.

_MOC de Referencia:_ [[MOC_Fundamentos_Sistemas]]