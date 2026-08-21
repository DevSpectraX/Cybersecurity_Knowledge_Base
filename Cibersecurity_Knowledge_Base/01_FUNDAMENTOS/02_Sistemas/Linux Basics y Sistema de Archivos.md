---
tags: [linux, comandos, permisos, administracion]
---
Dominar la navegación por la línea de comandos (CLI) de Linux y comprender cómo se gestionan los permisos de los archivos es un requisito obligatorio. En Linux, el principio de diseño fundamental es que **"todo es un archivo"** (desde un documento de texto hasta un proceso, un directorio o un disco duro).



---

## 1. Comandos Básicos de Navegación y Control

* **Saber dónde estás (Print Working Directory):**
```bash
pwd
```

* **Listar archivos (con detalles y 
ocultos):**
```bash
ls -la
```


* `-l`: Formato largo (muestra permisos, dueño, grupo, tamaño y fecha de modificación).
* `-a`: Muestra todos los archivos, incluidos los ocultos (los que empiezan por un punto, ej: `.bash_history`).


* **Cambiar de directorio:**
```bash
cd /ruta/del/directorio
# Ir al directorio personal (home) del usuario actual:
cd ~
# Volver al directorio anterior en el que estabas:
cd -
# Subir un directorio:
cd ../
```


* **Copiar, Mover y Eliminar:**
```bash
cp archivo.txt /ruta/de/destino/
mv archivo.txt /nueva/ruta/
rm archivo.txt
# Forzar la eliminación recursiva de una carpeta y todo su contenido:
rm -rf /ruta/de/la/carpeta/

```



---

## 2. Lectura y Filtrado de Archivos

* **Ver el contenido completo de un archivo en pantalla:**
```bash
cat archivo.txt

```


* **Ver el inicio o el final de un archivo (Muy útil para revisar archivos de registro o logs):**
```bash
head -n 10 archivo.log   # Muestra las primeras 10 líneas
tail -n 10 archivo.log   # Muestra las últimas 10 líneas
tail -f archivo.log      # Muestra los cambios del archivo en tiempo real

```


* **El comando de filtrado por excelencia: `grep`
Permite buscar cadenas de texto específicas dentro de archivos o salidas de comandos.
```bash
cat /etc/passwd | grep "root"
```

---

## 3. El Sistema de Permisos (UGO)

Cada archivo y carpeta en Linux pertenece a un **Usuario (User/Owner)** y a un **Grupo (Group)**. Los permisos de acceso se dividen y gestionan en tres niveles distintos:

1. **U**ser (El propietario individual del archivo).
2. **G**roup (Todos los usuarios que forman parte del grupo asignado al archivo).
3. **O**thers (Cualquier otro usuario del sistema que no sea el dueño ni pertenezca al grupo).

### Los Tres Bits de Permiso

* `r` (**Read** - Lectura): Permite leer el contenido del archivo o listar los elementos de una carpeta. (Valor numérico: **4**)
* `w` (**Write** - Escritura): Permite modificar el contenido del archivo o crear/borrar archivos dentro de una carpeta. (Valor numérico: **2**)
* `x` (**Execute** - Ejecución): Permite ejecutar el archivo si es un programa/script, o entrar a una carpeta usando el comando `cd`. (Valor numérico: **1**)

### Modificación de Permisos y Propietarios

* **Cambiar permisos (`chmod`):**
Se puede realizar utilizando la notación octal sumando los valores de los bits correspondientes a cada nivel (Propietario-Grupo-Otros):
```bash
# Asignar control total al dueño (4+2+1=7), lectura y ejecución al grupo (4+1=5), y ningún permiso a otros (0):
chmod 750 script.sh

```


* **Cambiar propietario y grupo (`chown`):**
```bash
# Cambiar el dueño al usuario 'root' y el grupo asignado a 'administradores':
sudo chown root:administradores archivo.txt

```



---

## 💀 Enfoque de Ciberseguridad: El Peligro del "777"

En auditorías reales o entornos de laboratorios, es crítico buscar archivos o scripts con permisos `777` (`rwxrwxrwx`). Esta configuración implica que **cualquier usuario** local del sistema operativo, sin importar sus privilegios, puede leer, escribir y modificar ese archivo.

Si un atacante con una shell de bajos privilegios detecta un script con permisos `777` que es ejecutado periódicamente por una cuenta con privilegios elevados (como `root` o un administrador de servicios), puede alterar su código interno para forzar al sistema a ejecutar comandos maliciosos en un contexto de altos privilegios, logrando una escalada de privilegios exitosa.

---

*MOC de Referencia:* [[MOC_Fundamentos_Sistemas]]