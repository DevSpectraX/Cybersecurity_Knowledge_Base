---
tags: [sistemas, windows, cli, cmd, powershell, enumeracion, post-explotacion]
---

Cuando obtienes acceso a una máquina Windows durante una auditoría de seguridad o un ejercicio de Red Team (ya sea a través de una Shell interactiva o una sesión de C2), la fase inmediata es la **Enumeración**. Necesitas recolectar información de forma rápida y silenciosa sobre el sistema operativo, los usuarios, las configuraciones de red y los privilegios para trazar una estrategia de escalada de privilegios o movimiento lateral.

A continuación se detalla el _Playbook_ de comandos reales utilizando la consola clásica (`cmd.exe`) y herramientas nativas de `PowerShell`.

## 💻 1. Enumeración del Sistema y Entorno

Lo primero es saber dónde estás parado, qué versión exacta de Windows corre la máquina y qué parches de seguridad (Hotfixes) faltan.

### 🔹 Consola Clásica (`cmd.exe`)

- **Obtener información básica del sistema, arquitectura y parches instalados:**
```DOS
systeminfo
```

- **Listar solo los parches de seguridad para buscar vulnerabilidades (Kernel Exploits):**
```DOS
wmic qfe get Caption,Description,HotFixID,InstalledOn
```

- **Ver las variables de entorno del sistema (rutas, arquitecturas, nombres de servidor):**
```DOS
set
```

### 🔹 PowerShell

- **Obtener detalles precisos del Sistema Operativo:**

```PowerShell
Get-ComputerInfo | Select-Object OsName, OsVersion, OsArchitecture, WindowsVersion
```
   
- **Listar actualizaciones del sistema ordenadas por fecha:**

```PowerShell
Get-HotFix | Sort-Object InstalledOn -Descending
```

## 👥 2. Enumeración de Usuarios, Grupos y Privilegios

Necesitas saber qué identidad tienes asignada en la sesión y qué puedes hacer dentro de las listas de control de acceso.

### 🔹 Consola Clásica (`cmd.exe`)

- **Ver el usuario actual y tus privilegios (Tokens / Privilegios asignados):**

```DOS
whoami /priv
```

> 💡 **Nota de Hacking:** Si en la salida ves privilegios como `SeImpersonatePrivilege` o `SeDebugPrivilege` con el estado _Enabled_ o _Disabled_, la máquina es altamente vulnerable a técnicas de escalada de privilegios locales (como la suite _JuicyPotato_ / _PrintSpoofer_).
    
- **Ver los grupos a los que pertenece tu usuario actual:**
```DOS
whoami /groups
```

- **Listar todos los usuarios de la máquina local:**

```DOS
net user
```

- **Ver información detallada de un usuario específico (ej: ver si su cuenta está expirada o bloqueada):**

```DOS
net user administrador
```

- **Listar los miembros del grupo Administradores locales:**

```DOS
net localgroup administrators
```

_(Nota: Si el sistema operativo está en español, debes cambiar el nombre a `net localgroup administradores`)._


### 🔹 PowerShell

- **Ver los privilegios del token actual de forma estructurada:**

```PowerShell
  [System.Security.Principal.WindowsIdentity]::GetCurrent().Privileges
```

- **Listar los usuarios locales y ver si están activos:**

```PowerShell
Get-LocalUser | Select-Object Name, Enabled, Description
```

- **Ver los miembros de un grupo local específico de forma limpia:**

```PowerShell
 Get-LocalGroupMember -Group "Administrators"
```    

## 🧰 3. Enumeración de Procesos, Software y Servicios

Buscamos software de terceros mal configurado, servicios vulnerables o la presencia de agentes de seguridad (Antivirus/EDR).

### 🔹 Consola Clásica (`cmd.exe`)

- **Listar procesos activos asociados al nombre del ejecutable y su PID:**

```DOS
tasklist /v
```

- **Buscar si un proceso específico (ej: un antivirus) se está ejecutando:**

```DOS
tasklist | findstr /i "defender"
```

- **Listar todos los servicios instalados, su estado y su tipo de inicio:**

```DOS
sc query type= service state= all
```


### 🔹 PowerShell

- **Listar procesos activos e identificar la ruta del ejecutable en el disco:**

```PowerShell
Get-Process | Where-Object {$_.Path -ne $null} | Select-Object Id, ProcessName, Path
```

- **Listar software de terceros instalado (útil para buscar exploits conocidos de aplicaciones):**

```PowerShell
Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, Publisher
```

- **Buscar servicios cuyo nombre contenga una palabra clave y ver bajo qué cuenta corren:**

```PowerShell
Get-CimInstance -ClassName Win32_Service | Where-Object {$_.Name -like "*sql*"} | Select-Object Name, State, StartName
```


## 🌐 4. Enumeración de Red y Conexiones

Mapear los sockets abiertos y las máquinas con las que habla el servidor nos permite identificar vectores de movimiento lateral.

### 🔹 Consola Clásica (`cmd.exe`)

- **Ver la configuración IP de todas las tarjetas de red y el servidor DNS:**

```DOS
ipconfig /all
```

- **Ver la tabla de enrutamiento local:**

```DOS
route print
```  

- **Ver todas las conexiones activas y puertos en escucha junto al PID del proceso dueño:**

```DOS
netstat -ano
```

- **Ver la tabla de caché ARP (máquinas locales con las que ha hablado recientemente):**

```DOS
arp -a
```


### 🔹 PowerShell

- **Listar conexiones de red activas filtrando únicamente por las que estén en escucha (`Listen`):**

```PowerShell
Get-NetTCPConnection -State Listen | Select-Object LocalAddress, LocalPort, OwningProcess
```

- **Hacer un escaneo rápido de ping a un rango interno usando PowerShell (sin herramientas externas):**

```PowerShell
1..254 | ForEach-Object { Test-Connection -ComputerName "192.168.1.$_" -Count 1 -ErrorAction SilentlyContinue } | Select-Object Address, Status
```


## 🌲 5. Reconocimiento de Active Directory (Desde una máquina del dominio)

Si la máquina auditada pertenece a un dominio corporativo, puedes interrogar al Controlador de Dominio desde la terminal.

### 🔹 Consola Clásica (`cmd.exe`)

- **Ver a qué dominio pertenece la máquina y cuál es el controlador de dominio de la sesión:**

```DOS
echo %USERDOMAIN%
echo %LOGONSERVER%
```

- **Listar todos los usuarios de todo el dominio de la empresa:**

```DOS
net user /domain
```

- **Ver qué usuarios pertenecen al grupo de Administradores del Dominio (Los objetivos máximos):**

```DOS
net group "Domain Admins" /domain
```


### 🔹 PowerShell (Sin requerir las herramientas de administración RSAT)

Utilizando la clase integrada de aceleradores de tipo de búsqueda de Active Directory, puedes evadir las restricciones de comandos protegidos:

- **Buscar el nombre del Bosque y el Dominio actual:**
```PowerShell
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
```

 
- **Enumerar los Controladores de Dominio de la infraestructura:**

```PowerShell
  [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().DomainControllers

```

_MOC de Referencia:_ [[MOC_Fundamentos_Sistemas]]