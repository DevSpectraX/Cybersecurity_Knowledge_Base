---
tags: [linux, fhs, directorios, estructura]
---
# Estructura de Carpetas en Linux y FHS

El **FHS (Filesystem Hierarchy Standard)** es el estándar que define la estructura de directorios y el contenido de cada uno en sistemas operativos tipo Unix. En Linux, **todo cuelga de la raíz `/`**. No existen los discos independientes como `C:` o `D:` de Windows; todo se monta bajo un único árbol de directorios.



---

## 📁 Directorios Clave para Ciberseguridad y Administración

Aquí están las carpetas que vas a inspeccionar el 99% de las veces cuando entres a auditar o administrar una máquina Linux:

### 1. `/etc` (Configuración del Sistema)
Contiene los archivos de configuración de todo el sistema operativo y de los servicios instalados.
* `⚠️ /etc/passwd`: Lista de todos los usuarios del sistema. Es legible por cualquier usuario (no contiene contraseñas, solo IDs, directorios *home* y rutas de shell). Ideal para enumerar usuarios válidos.
* `💀 /etc/shadow`: Contiene los **hashes de las contraseñas** de los usuarios. Solo es legible por el usuario `root`. Si consigues leer este archivo mediante una mala configuración o vulnerabilidad, puedes intentar crackear los hashes de forma offline.
* `/etc/cron*`: Directorios que contienen las tareas programadas del sistema (`cron jobs`). Revisar más información en [[Cron_Job]]

### 2. `/var` (Datos Variables)
Contiene archivos cuyo contenido se espera que cambie constantemente de tamaño o datos durante el funcionamiento normal del sistema, como logs, colas de correo y bases de datos.
* `🔍 /var/log`: El historial del sistema. Aquí residen archivos como `/var/log/auth.log` (intentos de inicio de sesión) o `/var/log/apache2/access.log` (logs del servidor web). Es vital para forense y detección de ataques.
* `🌐 /var/www`: El directorio por defecto donde se suelen alojar las aplicaciones y páginas web (ej. `/var/www/html`). 

### 3. `/tmp` y `/dev/shm` (Espacio Temporal)
Directorios diseñados para almacenar archivos temporales de los programas. Por defecto, cualquier usuario del sistema tiene permisos de lectura, escritura y ejecución en ellos.
* **Interés ofensivo:** Cuando se obtiene un acceso inicial con un usuario de bajos privilegios, se suele usar `/tmp` para descargar scripts de enumeración o binarios debido a sus permisos abiertos.
* **`/dev/shm`**: Funciona igual que `/tmp` pero corre directamente sobre la memoria RAM (Shared Memory). Lo que escribas ahí no toca el disco físico, lo que ofrece mayor velocidad y volatilidad.

### 4. `/home` y `/root` (Directorios Personales)
* `/home/[usuario]`: Carpetas personales de los usuarios normales del sistema. Aquí se almacenan sus configuraciones locales, archivos personales y claves SSH privadas (`~/.ssh/id_rsa`).
* `/root`: El directorio personal exclusivo del administrador supremo del sistema (`root`).

### 5. `/bin`, `/sbin`, `/usr/bin` (Los Binarios)
Contienen los comandos ejecutables del sistema necesarios para que el entorno funcione.
* `/bin` y `/usr/bin`: Comandos estándar y utilidades utilizables por todos los usuarios (`ls`, `cat`, `ping`, etc.).
* `/sbin` y `/usr/sbin`: Binarios de administración del sistema. Muchos de estos comandos requieren privilegios elevados (`sudo`) para ejecutarse correctamente.

---
*MOC de Referencia:* [[MOC_Fundamentos_Sistemas]]