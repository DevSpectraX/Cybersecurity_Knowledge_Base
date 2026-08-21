---
tags:
  - "#linux"
  - "#enumeracion"
  - "#hacking"
  - "#cheatsheet"
---


La enumeración es la fase más crítica tras obtener acceso inicial a un sistema. Consiste en recolectar de forma metódica información sobre el entorno, los usuarios y las configuraciones para identificar debilidades que permitan la escalada de privilegios.



---

## 1. Información del Sistema y Arquitectura

Saber ante qué tipo de máquina y Kernel nos enfrentamos permite buscar exploits conocidos (Kernel Exploits) o entender las limitaciones del entorno.

* **Ver la versión del Kernel y arquitectura exacta (32/64 bits):**
```bash
uname -a
```


- **Ver la distribución de Linux instalada y su versión:**
```bash
cat /etc/release
```

- **Ver las variables de entorno configuradas (Rutas del PATH, variables locales):**
```bash
env
```
 
 
- **Ver los sistemas de archivos montados y el espacio disponible:**
```bash
df -h
lsblk
```


## 2. Información de Usuarios y Privilegios

Identificar quiénes somos, en qué grupos estamos y qué otros usuarios reales existen en la máquina.

- **Ver la identidad del usuario actual, su UID, GID y los grupos a los que pertenece:**
```bash
id
```


- **Listar los privilegios de sudo permitidos para el usuario actual:**

```bash
sudo
```


- **Listar todos los usuarios del sistema (Extrayendo solo la primera columna):**

```bash
cut -d: -f1 /etc/passwd
```

- **Ver quién ha iniciado sesión en el sistema o los últimos accesos registrados:**

```bash
who last
```

## 3. Enumeración de Red y Conexiones

Buscar servicios internos ocultos desde el exterior o conexiones activas con otras máquinas de la red.

- **Ver las interfaces de red configuradas y sus direcciones IP:**
```bash
ip a
```


- **Listar puertos abiertos localmente y conexiones de red activas:**
```bash
ss -tuln
```
_(Parámetros: `-t` TCP, `-u` UDP, `-l` Listening, `-n` Numérico)_

- **Ver la tabla de rutas del sistema para entender a qué subredes llega la máquina:**
  ```bash
  ip route
  ```
  
- **Ver los hosts vecinos descubiertos en la red local (Tabla ARP):**

```bash
ip neigh
```

## 4. Búsqueda de Archivos y Malas Configuraciones

Localizar archivos con permisos inusuales o configuraciones peligrosas que puedan explotarse.

- **Buscar binarios con el bit SUID activo (Se ejecutan con los privilegios del dueño, comúnmente root):**

```bash
find / -perm -4000 -type f 2>/dev/null
```

- **Buscar archivos en los que nuestro usuario actual tenga permisos de escritura directos:**

```bash
find / -writable -type f 2>/dev/null
```

- **Buscar archivos modificados recientemente (Útil para detectar scripts que se ejecutan en segundo plano):**

```bash
find / -mmin -10 -type f 2>/dev/null
```

## 5. Tareas Programadas (Cron Jobs)

Los scripts automáticos que corre el sistema de forma periódica son uno de los vectores de escalada de privilegios más comunes si apuntan a archivos modificables.

- **Listar las tareas programadas configuradas para el usuario actual:**

```bash
crontab -l
```

- **Ver el archivo principal de configuración de tareas cron del sistema:**

```bash
cat /etc/crontab
```

- **Listar los directorios donde el sistema guarda las tareas programadas por horas, días o semanas:**

```bash
ls -la /etc/cron*
```


_MOC de Referencia:_ [[MOC_Fundamentos_Sistemas]]