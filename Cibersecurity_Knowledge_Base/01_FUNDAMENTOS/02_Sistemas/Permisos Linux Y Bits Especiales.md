---
tags: [linux, seguridad, permisos]
---
# Permisos en Linux y Bits Especiales

El acceso a archivos y carpetas en Linux se gestiona mediante tres tipos de usuarios: **Propietario (u)**, **Grupo (g)** y **Otros (o)**.

## Representación Simbólica y Octal
- `r` (Read / Lectura) = 4
- `w` (Write / Escritura) = 2
- `x` (Execute / Ejecución) = 1

*Ejemplo:* `chmod 755 archivo.sh` significa:
- Propietario: `7` (4+2+1 = rwx)
- Grupo: `5` (4+1 = r-x)
- Otros: `5` (4+1 = r-x)

## ⚠️ Bits Especiales (Cruciales para Hacking)

### 1. SUID (Set User ID) - Octal: 4000
Cuando se ejecuta un archivo con SUID, el proceso corre con los privilegios del **dueño del archivo** (a menudo `root`), no del usuario que lo lanza.
- **Cómo se ve:** `-rwsr-xr-x` (Fíjate en la `s` minúscula en los permisos del dueño).
- **Interés ofensivo:** Si encuentras un binario como `find`, `nano` o `bash` con SUID, puedes conseguir una Shell de root de forma directa.

### 2. SGID (Set Group ID) - Octal: 2000
Similar al SUID, pero el proceso corre con los privilegios del **grupo** propietario del archivo.
- **Cómo se ve:** `-rwxr-sr-x` (La `s` en la sección del grupo).

### 3. Sticky Bit - Octal: 1000
Se aplica a directorios (como `/tmp`). Asegura que solo el dueño de un archivo (o root) pueda borrarlo o renombrarlo, incluso si otros tienen permisos de escritura en esa carpeta.
- **Cómo se ve:** `drwxrwxrwt` (La `t` al final).