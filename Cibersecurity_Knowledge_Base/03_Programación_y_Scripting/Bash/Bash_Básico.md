https://youtu.be/RUorAzaDftg?list=PLlb2ZjHtNkpjgtjnZjXHQojIrXd4DxRNg&t=7626

> En Bash cuando queremos que algo NO SEA ALGO añadimos `!` antes de lo que queremos negar.

> Truco: Si pulsamos `Ctrl + R`, nos autocompletara los comando que hayamos ido usando en la terminal


## Expresiones Regulares (Regex)

Definen patrones de búsqueda complejos para encontrar, filtrar o reemplazar cadenas de texto específicas dentro de cualquier volumen de datos.

| **Sintaxis Especial** | **Para qué sirve**                      | **Explicación del truco**                                                                                                                                                  |
| --------------------- | --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `^texto`              | Anclaje de inicio (`^`).                | Busca líneas que **empiecen** estrictamente con la palabra indicada. Evita falsas coincidencias a mitad de línea.                                                          |
| `texto$`              | Anclaje de fin (`$`).                   | Busca líneas que **terminen** estrictamente con la palabra indicada.                                                                                                       |
| `.`                   | Comodín universal.                      | Coincide con **cualquier carácter individual** (letra, número o símbolo), excepto un salto de línea.                                                                       |
| `[a-z]` o `[0-9]`     | Clases de caracteres.                   | Coincide con **un solo carácter** que esté dentro del rango indicado (ej: cualquier letra minúscula o cualquier número).                                                   |
| `[^0-9]`              | Clase negada (`^` dentro de corchetes). | Coincide con cualquier carácter que **no** sea un número. El truco perfecto para descartar dígitos en un filtro.                                                           |
| `*`                   | Cuantificador cero o más.               | Indica que el carácter anterior puede aparecer **0, 1 o infinitas veces** consecutivas (ej: `col*or` encuentra "coor", "color" y "colllor").                               |
| `+`                   | Cuantificador uno o más.                | Indica que el carácter anterior debe aparecer **al menos una vez** obligatoriamente (ej: `col+or` encuentra "color" pero no "coor").                                       |
| `?`                   | Cuantificador opcional.                 | Indica que el carácter anterior puede estar o no estar (**0 o 1 vez**). Clave para buscar palabras en plural o singular (ej: `espanol?` encuentra "espanol" y "espanols"). |
| `{3}` o `{1,3}`       | Cuantificador de rango explícito.       | Define el número exacto o el rango de repeticiones permitidas. `[0-9]{1,3}` busca bloques numéricos de uno, dos o tres dígitos (esencial para IPs).                        |
| `\b`                  | Límite de palabra (_Boundary_).         | Asegura que el patrón sea una **palabra completa aislada** y no parte de otra más larga (ej: `\badmin\b` encuentra "admin", pero ignora "administrador").                  |
| `\`                   | Carácter de escape.                     | Anula el poder especial de los símbolos de Regex para buscarlos como texto literal. Si quieres buscar un punto real en el disco duro, debes escribir `\.`.                 |
| `(txt\|md)`           | Grupo de captura y operador OR (`\|`).  | Agrupa opciones alternativas. Busca archivos que terminen tanto en `.txt` **o** en `.md`.                                                                                  |

#### 💡Los atajos de clases nativas (`\d`, `\w`, `\s`)

Cuando usas expresiones regulares extendidas (como en `grep -E` o en scripts de Python/Perl), puedes sustituir los corchetes largos por estos atajos universales que te ahorran muchísimo espacio:

- **`\d`** = Equivale a `[0-9]` (Cualquier **d**ígito numérico).
    
- **`\w`** = Equivale a `[a-zA-Z0-9_]` (Cualquier carácter alfanumérico o guion bajo / **w**ord).
    
- **`\s`** = Equivale a un **s**pacio en blanco, tabulador o salto de línea.
    

Si los pones en **mayúscula**, hacen exactamente lo contrario:

- **`\D`** = Cualquier cosa que **no** sea un número.
    
- **`\W`** = Cualquier cosa que **no** sea una letra o número (símbolos como `@`, `#`, `$`).



## Listar `ls`

_Inspeccionar el contenido de los directorios._

| **Comando + Banderas** | **Qué añade cada bandera**   | **Resultado / Utilidad en Hacking**                                                                                                                                    |
| ---------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ls`                   | _Ninguna (Comando base)_     | Muestra solo los nombres de los archivos visibles en formato compacto.                                                                                                 |
| `ls -l`                | **`-l`** (Formato largo)     | Muestra los detalles de seguridad: **permisos (Read/Write/Execute)**, propietario, grupo y tamaño.                                                                     |
| `ls -la`               | **`-a`** (All / Todo)        | Muestra absolutamente todo, incluyendo **archivos ocultos** (`.`) que suelen esconder configuraciones o claves SSH.                                                    |
| `ls -lat`              | **`-t`** (Time / Tiempo)     | Ordena los archivos por **fecha de modificación**. Los creados o editados más recientemente aparecen arriba del todo.                                                  |
| `ls -latr`             | **`-r`** (Reverse / Inverso) | Invierte el orden temporal. Los archivos modificados **más recientemente aparecen abajo del todo** (ideal para no tener que hacer scroll hacia arriba en la terminal). |

## Leer `cat`

_Volcar el contenido de los archivos en la pantalla._

|**Comando + Banderas**|**Qué añade cada bandera**|**Resultado / Utilidad en Hacking**|
|---|---|---|
|`cat archivo`|_Ninguna (Comando base)_|Escupe todo el contenido del archivo de texto plano de golpe en la terminal.|
|`cat -n archivo`|**`-n`** (Number / Números)|**Enumera todas las líneas** del archivo. Es vital cuando estás buscando un fallo en un script y la terminal te dice: _"Error en la línea 42"_.|
|`cat -E archivo`|**`-E`** (Ends / Finales)|Añade un símbolo de **`$` al final de cada línea**. Sirve para detectar espacios ocultos o saltos de línea invisibles que rompen exploits.|
|`cat -s archivo`|**`-s`** (Squeeze / Exprimir)|**Saca el aire del archivo:** si hay varias líneas en blanco seguidas, las comprime en una sola línea vacía para limpiar la vista.|
|`cat -v archivo`|**`-v`** (Visual / No imprimibles)|Muestra caracteres de control invisibles (como los caracteres `^M` de los archivos creados en Windows que rompen los scripts de Linux).|

A veces no necesitas banderas, sino cambiar la sintaxis para evitar que el comando se rompa con nombres de archivos conflictivos o con espacios:

| **Sintaxis Especial**       | **Para qué sirve**                     | **Explicación del truco**                                                                                                          |
| --------------------------- | -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `cat "nombre con espacios"` | Evitar errores de argumento            | Las comillas obligan a Bash a tratar toda la frase como un único archivo.                                                          |
| `cat ./-`                   | Leer el archivo llamado `-`            | El `./` le dice a `cat` que busque un archivo real en el directorio actual, evitando que confunda el guion con una bandera vacía.  |
| `cat $(pwd)/-`              | Leer el archivo llamado `-` (Absoluto) | Reemplaza el paréntesis por la ruta completa del sistema (`/home/user/...`) para localizar el archivo conflictivo de forma exacta. |
| `cat /ruta/*`               | Lectura con comodín                    | El asterisco actúa como un "lee todo lo que haya aquí dentro". Si solo hay un archivo en la carpeta, lo abrirá directamente.       |

## Buscar `find`

_Buscar archivos y directorios._


| **Comando + Banderas**                 | **Qué añade cada bandera**                                                                                                                                                                                                                                                        | **Resultado / Utilidad en Hacking**                                                                                                                                    |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `find archivo`                         | Ninguna (Comando base)                                                                                                                                                                                                                                                            | Busca archivos con ese nombre                                                                                                                                          |
| `find .`                               | `.` (De aquí en adelante)                                                                                                                                                                                                                                                         | Busca archivos desde la ruta actual hacia adentro de forma recursiva                                                                                                   |
| `find . -type f`                       | `-type f` (Solo ficheros)                                                                                                                                                                                                                                                         | Lista solo ficheros                                                                                                                                                    |
| `find . -printf "\t%p\t%u\t%g\t%m\n"`  | `\t` (Mostrar de forma tabulada)<br><br>  <br><br>`\n` (Aplica un salto de línea)<br><br>  <br><br>`%p` (Mostrar la ruta absoluta)<br><br>  <br><br>`%u` (Mostrar usuario propietario)<br><br>  <br><br>`%g` (Mostrar el grupo asignado)<br><br>  <br><br>`%m` (Mostrar permisos) | Podemos ver de manera cómoda los archivos y sus permisos                                                                                                               |
| `find . -name .estoyoculto`            | `-name` (busca por nombre)                                                                                                                                                                                                                                                        | Busca hacia adentro hasta que encuentra un archivo con ese nombre aunque este oculto                                                                                   |
| `find . -type f -executable`           | Filtrar por ejecutables.                                                                                                                                                                                                                                                          | Busca solo archivos que tu usuario actual tiene permiso de ejecutar (binarios y scripts listos para correr).                                                           |
| `find . -type f -readable`             | Filtrar por lectura.                                                                                                                                                                                                                                                              | Filtra la búsqueda para mostrar solo archivos que tu usuario puede leer, evitando archivos bloqueados.                                                                 |
| `find . -type f -writable`             | Filtrar por escritura.                                                                                                                                                                                                                                                            | El santo grial en escalada de privilegios. Busca archivos que puedes modificar. Si encuentras un script del sistema con esta bandera, puedes meterle código malicioso. |
| `find / -writable -type d 2>/dev/null` | Buscar carpetas modificables.                                                                                                                                                                                                                                                     | Encuentra directorios (como /tmp o /dev/shm) donde cualquier usuario puede escribir y guardar sus herramientas de explotación.                                         |
| `find . -type f -size 0`               | `-size 0` (Tamaño exacto)                                                                                                                                                                                                                                                         | Busca archivos completamente vacíos (0 bytes). Útil para detectar configuraciones corruptas o archivos de intercambio colgados.                                        |
| `find . -type f -size +100M`           | `-size +[peso]` (Tamaño mayor que)                                                                                                                                                                                                                                                | Busca archivos que pesen **más de** la cantidad indicada. Ideal para localizar copias de seguridad (.bak, .tar.gz) u otros archivos pesados olvidados.                 |
| `find . -type f -size -10k`            | `-size -[peso]` (Tamaño menor que)                                                                                                                                                                                                                                                | Busca archivos que pesen **menos de** la cantidad indicada. Excelente para filtrar y quedarte solo con pequeños scripts de texto o exploits en C.                      |
| `find -user jose -group jose`          | `-user`(Filtra por nombre de useario) `-group`(filtrar por un nombre de grupo)                                                                                                                                                                                                    |                                                                                                                                                                        |

>📁 **Unidades clave para el tamaño (`-size`):** `c` = Bytes (ej: `50c`), `k` = Kilobytes (ej: `10k`), `M` = Megabytes (ej: `100M`), `G` = Gigabytes (ej: `2G`)


## Concatenar comandos `xargs`

_Pasa la salida de un comando anterior como si fuera el argumento de entrada para el siguiente comando, permitiendo automatizar tareas masivas en una sola línea.

| **Sintaxis Especial**                      | **Para qué sirve**                                                         | **Explicación del truco**                                                           |
| ------------------------------------------ | -------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **`\| xargs`**                             | Utilizando xargs solamente, nos elimina los saltos de linea                |                                                                                     |
| **`find . -type f \| xargs grep "leave"`** | `\| xargs comando` (aplica a la salida del comando anterior, otro comando) | Busca la palabra "leave" a partir de la salida del comando find o de cualquier otro |


## Cambiar permisos `chmod`

_Cambia permisos a los distintos tipos de usuarios_

| **Sintaxis Especial**     | **Para qué sirve**                                               | **Explicación del truco**            |
| ------------------------- | ---------------------------------------------------------------- | ------------------------------------ |
| **chmod 640 file.txt`**   | (propietario -> 6, grupo ->4, otros ->0)                         |                                      |
| **`chmod o-rw file.txt`** | **`o`**(otros)  **`-rw`**(quita permisos de lectura y escritura) | También podemos dar mediante **`+`** |


## Cambiar atributos (cambiar permisos)`chattr`

_Añade o quita atributos especiales._

| **Sintaxis Especial** | **Para qué sirve**                         | **Explicación del truco**                                                                                                                                                                                                     |
| --------------------- | ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `chattr +i archivo`   | **`i`** (Immutable / Inmutable)            | **La bandera más importante.** El archivo no se puede borrar, renombrar, modificar, ni se le pueden crear enlaces. **Ni siquiera `root` puede tocarlo** hasta que se le quite la bandera con `-i`.                            |
| `chattr +a archivo`   | **`a`** (Append Only / Solo añadir)        | El archivo no se puede borrar ni modificar lo que ya tiene escrito, pero **sí se le puede añadir texto nuevo al final**. Ideal para proteger archivos de registros (_Logs_) para que un atacante no pueda borrar sus huellas. |
| `chattr +s archivo`   | **`s`** (Secure Deletion / Borrado seguro) | Cuando borras el archivo, los bloques del disco duro **se sobrescriben con ceros inmediatamente**. Evita que un forense informático pueda recuperar el archivo borrado usando herramientas de recuperación.                   |
| `chattr +u archivo`   | **`u`** (Undeletable / Recuperable)        | Lo contrario a la anterior. Si el archivo se borra, el sistema guarda una copia de sus datos para que pueda ser **recuperado fácilmente** (_Undelete_).                                                                       |
| `chattr +c archivo`   | **`c`** (Compressed / Comprimido)          | El Kernel de Linux **comprime el archivo automáticamente** en el disco duro. Cuando lo lees, se descomprime de forma transparente. Útil para ahorrar espacio discretamente.                                                   |
| `chattr +R carpeta`   | **`-R`** (Recursive / Recursivo)           | _(Nota: Esta es una opción de comando, va antes del más/menos)_. Aplica el atributo que elijas a la carpeta y a **todo lo que haya dentro** de ella. Ejemplo: `chattr -R +i /var/www/html`.                                   |

## Localizar Binarios `which`

Busca y muestra la ruta exacta del disco duro donde está instalado un programa o comando.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`which comando`|Busca la ruta exacta del ejecutable base.|Si no devuelve nada, significa que la herramienta no está instalada.|
|`which -a comando`|Muestra todas las rutas del binario encontradas.|Útil si sospechas que hay varias versiones interfiriendo entre sí.|

## Identificar Archivos `file`

Analiza las cabeceras internas de un archivo en hexadecimal y mira el numero mágico(Los primeros hexadecimales de la primera linea) para decirte qué tipo de archivo es realmente, ignorando su extensión.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`file archivo`|Analiza el archivo base y te dice su tipo real.|Detecta si una supuesta "imagen" es en verdad un script malicioso.|
|`file -b archivo`|Breve: Quita el nombre del archivo de la salida.|Te da solo el tipo de datos limpio, ideal para guardar en variables.|
|`file -i archivo`|Muestra la información en formato MIME.|Estructura la salida bajo el estándar web (Ej: text/plain).|
|`file -z archivo.zip`|Mira dentro de archivos comprimidos.|Identifica qué contiene un paquete sin necesidad de descomprimirlo|

## Medición de Tiempos `time`

Mide cuánto tiempo tarda en ejecutarse un comando o script, desglosando el uso de recursos entre el sistema y la CPU.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`time comando`|Mide el tiempo de ejecución.|Muestra al finalizar el comando tres métricas: `real` (tiempo total transcurrido), `user` (tiempo en CPU de usuario) y `sys` (tiempo en CPU del sistema).|

## Sustitución de Comandos `$(pwd)`

Ejecuta un comando en segundo plano y mete su resultado de texto directamente en esa misma posición de la línea de comandos.

| **Sintaxis Especial** | **Para qué sirve**         | **Explicación del truco**                                                                                                                                                                                                |
| --------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `pwd`                 | Imprime la ruta actual.    | Devuelve como texto la ruta exacta de la carpeta donde estás parado (Ej: `/home/user/Descargas`).                                                                                                                        |
| `$(pwd)`              | Sustituye la ruta en vivo. | Bash primero ejecuta lo que hay dentro del paréntesis (`pwd`), coge el texto resultante y lo "pega" ahí mismo. Si estás en `/etc`, escribir `cat $(pwd)/-` se convierte automáticamente para el sistema en `cat /etc/-`. |


## Editor de Flujo `sed`

Modifica, busca, reemplaza o borra texto de forma automática dentro de un archivo o flujo de datos sin necesidad de abrirlo manualmente.

| **Sintaxis Especial**                  | **Para qué sirve**                      | **Explicación del truco**                                                                                                                            |
| -------------------------------------- | --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sed 's/falso/verdadero/' archivo`     | `s/buscar/reemplazar/` (Sustitución)    | Busca la palabra "falso" y la cambia por "verdadero" **solo la primera vez** que aparezca en cada línea.                                             |
| `sed 's/falso/verdadero/g' archivo`    | Flag `g` (Sustitución Global)           | Modifica **todas** las apariciones de la palabra en todo el archivo. Ideal para cambiar IPs o nombres de usuario masivamente.                        |
| `sed -i 's/falso/verdadero/g' archivo` | `-i` (_In-place_ / Edición real)        | **Peligroso.** Aplica los cambios directamente sobre el archivo original guardándolo en el disco duro, en lugar de solo mostrarlo por pantalla.      |
| `sed '5s/falso/verdadero/' archivo`    | `[N]s/...` (Sustitución en línea N)     | Modifica la palabra "falso" únicamente si se encuentra de forma estricta en la **línea 5** del documento.                                            |
| `sed '/secreto/d' archivo`             | `/patrón/d` (_Delete_ / Borrado)        | Busca cualquier línea que contenga la palabra "secreto" y la **elimina por completo** del flujo de texto.                                            |
| `sed -n '5,10p' archivo`               | `-n` (Silenciar salida) y `p` (_Print_) | Bloquea la salida estándar e imprime **únicamente el rango de líneas** del 5 al 10. El truco perfecto para extraer fragmentos de código específicos. |


## Buscador de Patrones `grep`

Busca líneas que contengan una cadena de texto o expresión regular específica dentro de uno o varios archivos, filtrando la salida de forma masiva.

| **Sintaxis Especial**                                       | **Para qué sirve**                                                                                                | **Explicación del truco**                                                                                                                                                   |
| ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `grep "patrón" archivo`                                     | Búsqueda simple.                                                                                                  | Muestra únicamente las líneas del archivo que contienen la palabra exacta que estás buscando.                                                                               |
| `grep -i "pAtrÓn" archivo`                                  | Ignorar mayúsculas (`-i`).                                                                                        | Hace que la búsqueda no distinga entre mayúsculas y minúsculas. Esencial en CTFs porque encuentra palabras como `Flag`, `FLAG` o `flag`.                                    |
| `grep -v "error" archivo`                                   | Invertir la búsqueda (`-v`).                                                                                      | Muestra todas las líneas que **no** contienen la palabra indicada. El truco perfecto para limpiar _logs_ eliminando el ruido visual molesto.                                |
| `grep -r "password" /etc/`                                  | Búsqueda recursiva (`-r`).                                                                                        | Busca el texto dentro de todos los archivos de la carpeta indicada y de todas sus subcarpetas. Clave para encontrar credenciales expuestas en el sistema.                   |
| `grep -n "admin" archivo`                                   | Mostrar número de línea (`-n`).                                                                                   | Añade el número de línea exacto donde se encontró la coincidencia. Te ahorra tiempo al abrir el archivo para editarlo.                                                      |
| `grep -c "login" archivo`                                   | Contar coincidencias (`-c`).                                                                                      | En lugar de mostrar las líneas, te devuelve el número total de veces que aparece la palabra. Útil para estadísticas rápidas de ataques.                                     |
| `grep -E "^paco\|root" archivo`                             | Expresiones regulares extendidas (`-E`), Aquí estaria buscando lineas que empiezan con paco y que contengan root. | Activa el uso de Regex avanzado (equivalente a usar `egrep`). Permite buscar patrones complejos como estructuras de IPs, correos o hashes.                                  |
| `grep -A 2 -B 1 "secreto" archivo`                          | Mostrar contexto (`-A` / `-B`).                                                                                   | Muestra 1 líneas antes (`-B 1`) y 2 líneas después (`-A 2`) de la coincidencia encontrada. Ideal para ver el código que rodea a una función vulnerable.                     |
| `grep -C 2 "secreto"`                                       | `-C` (2 líneas por encima y 2 líneas por debajo)                                                                  | Muestra 2 líneas por encima y 2 líneas por debajo de la coincidencia.                                                                                                       |
| `grep -oE "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}"` | Extraer solo la coincidencia (`-o`).                                                                              | Descarta el resto de la línea y te devuelve **únicamente** el texto exacto que coincide con el patrón. En este ejemplo, extrae limpiamente todas las IPs de un texto sucio. |
>El `-E` podríamos omitirlo pero entonces deberíamos escapar el pipe usando `\` justo antes del pipe.

## Procesador de Textos `awk`

Un potente lenguaje de programación en miniatura diseñado para procesar, filtrar y manipular datos estructurados en columnas o tablas directamente desde la terminal.

| **Sintaxis Especial**                  | **Para qué sirve**                       | **Explicación del truco**                                                                                                                                                                                              |
| -------------------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `awk '{print $1}' archivo`             | Imprimir la primera columna.             | Por defecto, `awk` divide cada línea usando los espacios en blanco. `$1` representa la primera columna, `$2` la segunda, y `$0` la línea entera.                                                                       |
| `awk -F ":" '{print $1}' /etc/passwd`  | Cambiar el delimitador de campos (`-F`). | Le dice a `awk` qué carácter usar para separar las columnas. En este ejemplo, usa los dos puntos (`:`) para extraer limpiamente los nombres de usuario del sistema.                                                    |
| `awk '/rocky/' data.txt`               | Busca e imprime líneas completas         | Busca la palabra rocky en el archivo data.txt y te da la linea completa                                                                                                                                                |
| `awk '/admin/ {print $3}' archivo`     | Filtrar por patrón antes de actuar.      | Funciona como un `grep` integrado: busca únicamente las líneas que contengan la palabra "admin" y, de esas líneas, te imprime solo la tercera columna.                                                                 |
| `awk '{print $NF}' archivo`            | Imprimir la última columna (`$NF`).      | `NF` es una variable interna que guarda el **N**úmero de **F**ields (columnas) de la línea. Usar `$NF` te devuelve siempre el último elemento, sin importar si unas líneas tienen más columnas que otras.              |
| `awk 'NR==5 {print $0}' archivo`       | Filtrar por número de línea (`NR`).      | `NR` significa **N**umber of **R**ecords (número de línea actual). Con `NR==5` obligas a `awk` a interactuar únicamente con la quinta línea de todo el flujo de texto.                                                 |
| `awk 'NF > 3' archivo`                 | Filtrar por cantidad de columnas.        | Muestra solo aquellas líneas que tengan más de 3 columnas de datos. Es un truco excelente para limpiar salidas de texto rotas o incompletas.                                                                           |
| `awk '{print NR, $0}' archivo`         | Enumerar las líneas en vivo.             | Pone el número de la línea actual (`NR`) justo al principio de cada fila antes de imprimir el texto original (`$0`), imitando el comportamiento de `nl` o `grep -n`.                                                   |
| `awk -F "." '{print $(NF-1)}' archivo` | Imprimir la penúltima columna.           | Al restar uno a la variable `NF`, viajas una columna hacia atrás. Si filtras una lista de dominios web usando el punto (`.`) como separador, este truco te extraerá el nombre del dominio ignorando el `.com` o `.es`. |
Podemos usarlo junto con _pipe_ `rev` para invertir totalmente la linea



## Redirección de Errores `2>`

Controla, desvía o silencia de forma selectiva los mensajes de error generados por los comandos para limpiar la salida de la terminal.

- **`0` (STDIN):** La entrada de datos (tu teclado).
    
- **`1` (STDOUT):** La salida estándar (los resultados buenos que se muestran en pantalla).
    
- **`2` (STDERR):** La salida de errores (los mensajes de "Permiso denegado" o "Archivo no encontrado" que también van a la pantalla).
    

Cuando usas `2>`, le estás diciendo al sistema: _"Coge solo el canal 2 (los errores) y envíalos a otra parte para que no me manchen la pantalla"_.

| **Sintaxis Especial**       | **Para qué sirve**                  | **Explicación del truco**                                                                                                                                                                                        |
| --------------------------- | ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `comando 2> errores.txt`    | Guardar errores en un archivo.      | Separa los fallos del resultado bueno. El resultado correcto se ve en pantalla, pero todos los errores se guardan silenciosamente dentro de `errores.txt`.                                                       |
| `comando 2>/dev/null`       | **Silenciar errores por completo.** | Envía el canal 2 a `/dev/null` (el "agujero negro" de Linux, donde todo lo que entra se destruye). Es el truco definitivo al usar `find /` para que las líneas de "Permiso denegado" no te colapsen la terminal. |
| `comando > output.txt 2>&1` | Fusionar todos los canales.         | El símbolo `2>&1` le dice al sistema: _"Redirige el canal 2 (errores) al mismo sitio donde esté yendo el canal 1 (salida estándar)"_. Guarda tanto los aciertos como los fallos juntos en el archivo.            |
| `comando &> output.txt`     | Atajo de fusión moderna.            | Hace exactamente lo mismo que el truco anterior (`2>&1`), pero de forma mucho más corta y rápida de escribir. Envía absolutamente todo a un único archivo.                                                       |


## Liberar Terminal `> /dev/null 2>&1`

Desvía de forma absoluta tanto las salidas correctas como los mensajes de error de un programa hacia el agujero negro del sistema, manteniendo la terminal limpia y utilizable.

| **Sintaxis Especial**        | **Para qué sirve**                   | **Explicación del truco**                                                                                                                                                                                                                 |
| ---------------------------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `firefox > /dev/null`        | Silenciar la salida estándar (`1>`). | Coge todos los mensajes ordinarios de Firefox (el canal 1) y los manda a `/dev/null` para que no salgan en pantalla. **si no pones ningún número delante del símbolo `>`, el sistema asume automáticamente que te refieres al 1(STDOUT)** |
| `... 2>&1`                   | Fusionar errores con la salida.      | Apunta el canal 2 (errores) hacia donde ya esté apuntando el canal 1. Como el canal 1 va al agujero negro, los errores también se destruyen.                                                                                              |
| `firefox > /dev/null 2>&1 &` | **El truco completo (con `&`).**     | Si añades un **`&`** al final del todo, envías el proceso a ejecutarse en **segundo plano** (_background_). Firefox se abre, la terminal se queda muda y tú puedes seguir usándola para escribir otros comandos inmediatamente.           |
>El problema que genera que cree algo en segundo plano, es que pasa a ser hijo del elemento que lo haya creado, por lo tanto si cierras el elemento padre, también se cierra el hijo. Para transformar el hijo a un proceso independiente(para evitar que se cierre al cerrar la consola), se utiliza el comando **`diswon`**


## Independizar Procesos `disown`

Rompe la relación entre la terminal actual y los procesos que se están ejecutando en segundo plano para evitar que se cierren al salir de la sesión.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`disown`|Independizar el último proceso.|Elimina el último comando que mandaste al segundo plano de la lista de tareas (_jobs_) de la terminal. Al cerrar la consola, ese programa seguirá vivo.|
|`disown -a`|Independizar absolutamente todo (`-a`).|Rompe los lazos con **todos** los procesos que tengas corriendo en segundo plano en esa terminal de una sola vez.|
|`disown -h %1`|Mantener en la lista pero proteger (`-h`).|No elimina el proceso número 1 de la lista, pero le pone una marca de protección (**h**old). Si la terminal se cierra, no le enviará la señal de muerte (`SIGHUP`).|



## Contador de Palabras `wc`

Cuenta de forma rápida el número de líneas, palabras, caracteres o bytes que tiene un archivo también puede usarse mediante _pipe_, siendo una herramienta fundamental para contar resultados en hacking.

| **Sintaxis Especial** | **Para qué sirve**                   | **Explicación del truco**                                                                                                                                       |
| --------------------- | ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `wc archivo`          | Conteo estándar (Por defecto).       | Te devuelve tres números de golpe: el total de **líneas**, el total de **palabras** y el tamaño total en **bytes** del archivo.                                 |
| `wc -l archivo`       | Contar únicamente líneas (`-l`).     | **El flag más usado en hacking.** Cuenta cuántas filas tiene el texto. Ideal para saber cuántas contraseñas tiene un diccionario o cuántas IPs has descubierto. |
| `wc -w archivo`       | Contar únicamente palabras (`-w`).   | Cuenta cadenas de texto separadas por espacios. Útil para verificar la cantidad de argumentos o términos en un reporte.                                         |
| `wc -c archivo`       | Contar únicamente bytes (`-c`).      | Te dice el tamaño exacto en bytes. Como cada letra equivale a 1 byte en texto plano, sirve para medir de forma estricta el tamaño de un exploit o payload.      |
| `wc -m archivo`       | Contar únicamente caracteres (`-m`). | A diferencia de los bytes, este flag cuenta caracteres reales (detecta correctamente caracteres multi-byte como emojis o tildes).                               |


## Ordenador de Líneas `sort`

Ordena las líneas de un archivo de texto o de la salida de un comando anterior de forma alfabética, numérica o inversa para organizar la información de forma estructurada.

| **Sintaxis Especial**             | **Para qué sirve**              | **Explicación del truco**                                                                                                                                                                               |
| --------------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sort archivo`                    | Ordenación estándar.            | Ordena todas las líneas del archivo en orden alfabético ascendente (de la A a la Z) y por orden de caracteres.                                                                                          |
| `sort -r archivo`                 | Ordenación inversa (`-r`).      | Invierte el orden del resultado, mostrando las líneas de la Z a la A o de mayor a menor.                                                                                                                |
| `sort -n archivo`                 | Ordenación numérica (`-n`).     | **Fundamental.** Evita que Linux ordene los números de forma alfabética (donde el `10` iría antes que el `2`). Con `-n`, el `2` va antes que el `10`.                                                   |
| `sort -u archivo`                 | Eliminar duplicados (`-u`).     | Ordena el archivo y, al mismo tiempo, borra todas las líneas que sean exactamente iguales. Te devuelve solo valores **u**nicos.                                                                         |
| `sort -M archivo`                 | Ordenar por meses (`-M`).       | Capaz de ordenar fechas de forma lógica reconociendo los nombres de los meses (ENE, FEB, MAR...).                                                                                                       |

### 💡 El combo imprescindible: `sort` + `uniq`

`uniq` tiene un problema: **solo borra duplicados si las líneas repetidas están una junta a la otra**. Por eso, el texto siempre debe pasar por `sort` primero.




En este caso mostrara de data.txt solo las líneas únicas sin repeticiones, el comando `-u` elimina de manera estricta los repetidos.
```bash
cat data.txt | sort | uniq -u
```

- **Contar cuántas veces se repite cada IP en un log de ataques (de mayor a menor):**

```
cat accesos.log | awk '{print $1}' | sort | uniq -c 
```
- `sort`: Junta todas las IPs idénticas.
- `uniq -c`: Borra los duplicados y añade una columna a la izquierda indicando **c**uántas veces aparecía esa IP.

## Extractor de Texto `strings`

Filtra y extrae secuencias de caracteres imprimibles incrustadas dentro de archivos binarios o ejecutables, permitiendo analizar su contenido sin necesidad de descompilarlo.

| **Sintaxis Especial** | **Para qué sirve**                       | **Explicación del truco**                                                                                                                                          |
| --------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `strings programa`    | Extracción estándar, palabra por palabra | Vuelca en la pantalla todas las palabras legibles del binario. El truco perfecto en CTFs para encontrar _flags_ o contraseñas quemadas en el código (_hardcoded_). |



## Control de Extremos `head` y `tail`

Estos comandos se utilizan para cortar y visualizar de forma selectiva las partes superiores (`head`) o inferiores (`tail`) de un flujo de texto o archivo, evitando tener que cargar archivos gigantescos en la memoria de la terminal.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`head archivo`|Ver el principio (Por defecto).|Muestra las primeras **10 líneas** del archivo. Es el estándar rápido para comprobar la estructura de una tabla o un documento.|
|`head -n 5 archivo`|Modificar cantidad de líneas (`-n`).|Te muestra exactamente el número de líneas que le pidas (en este caso, las primeras 5). También se puede escribir abreviado como `head -5`.|
|`tail archivo`|Ver el final (Por defecto).|Muestra las últimas **10 líneas** del archivo. Ideal para ver las acciones más recientes grabadas en un sistema.|
|`tail -n 20 archivo`|Modificar cantidad de líneas en el final.|Te muestra únicamente las últimas 20 líneas del documento (abreviado: `tail -20`).|
|`tail -f /var/log/auth.log`|**Monitorear en tiempo real (`-f`).**|Deja la terminal abierta y **esperando**. Cada vez que el sistema escriba una línea nueva en el archivo de _log_, la verás aparecer en pantalla al instante. Es el comando rey para vigilar ataques e inicios de sesión en vivo.|
|`tail -n +12 archivo`|Empezar desde una línea concreta.|El signo `+` cambia el comportamiento: en lugar de darte las últimas líneas, **se salta las primeras 11** y te muestra todo el contenido desde la línea 12 hasta el final.|

### 💡 El truco del "Sándwich" (Extraer una línea exacta)

¿Qué pasa si necesitas extraer únicamente la **línea número 15** de un diccionario de contraseñas de 1 millón de filas? Puedes combinar `head` y `tail` en una sola tubería para hacer un corte quirúrgico perfecto sin usar herramientas pesadas:

Bash

```
head -n 15 data.txt | tail -n 1
```

- **`head -n 15`**: Coge las primeras 15 líneas del archivo y descarta el resto.
    
- **`tail -n 1`**: De ese grupo de 15 líneas que le han entrado, se queda únicamente con la **última**. El resultado en tu pantalla será, de forma exclusiva, la línea 15.



## Atajo de Historial `!$` (El último argumento)

Reutiliza instantáneamente el último parámetro o ruta del comando inmediatamente anterior para agilizar la escritura en la terminal.

| **Sintaxis Especial**                                                | **Para qué sirve**                | **Explicación del truco**                                                                                                                                                           |
| -------------------------------------------------------------------- | --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `!$`                                                                 | Invocar el último argumento.      | Al pulsar Enter, la terminal sustituye automáticamente `!$` por la última palabra o ruta que escribiste en el comando de arriba.                                                    |
| `mkdir -p /var/www/html/wp-content/uploads`<br><br>  <br><br>`cd !$` | Combinación de navegación rápida. | Creas una ruta de carpetas larguísima y, en la siguiente línea, pones `cd !$` para meterte dentro de ella directamente sin tener que copiar y pegar con el ratón.                   |


### 💡 El truco alternativo: El atajo de teclado físico

Si no quieres escribir `!$` y prefieres ver visualmente cómo se pega el texto antes de darle al Enter, puedes usar este atajo de teclado nativo en tu terminal:

- Presiona **`Alt` + `.`** (o `Esc` y luego `.`)
    

Cada vez que pulses esa combinación de teclas, la terminal irá rescatando el último argumento de los comandos anteriores hacia atrás en tu historial.


## Editor de Texto `nano`

Un editor de texto en consola, rápido y ligero, integrado por defecto en casi todas las distribuciones de Linux. Es la herramienta estándar para modificar archivos de configuración o escribir scripts rápidos sin salir de la terminal.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`nano archivo.txt`|Abrir o crear un archivo.|Abre el archivo indicado. Si el archivo no existe en esa carpeta, `nano` te abrirá un lienzo en blanco y lo creará automáticamente en cuanto guardes.|
|`nano -w archivo.conf`|Desactivar envoltura de línea (`-w`).|**Esencial para configurar servidores.** Evita que `nano` corte las líneas largas de código y les meta saltos de línea invisibles que podrían romper tus scripts o archivos de configuración.|
|`nano -v archivo.log`|Modo de solo lectura (`-v`).|Abre el archivo en modo "vista" (no te deja modificar nada). Es perfecto para inspeccionar _logs_ o códigos ajenos de forma segura, evitando que borres algo por error con un descuido del teclado.|
|`nano -c archivo.py`|Mostrar posición del cursor (`-c`).|Muestra constantemente abajo en la pantalla la línea exacta y el número de carácter en el que estás situado. Vital para encontrar errores de sintaxis cuando un compilador te dice: _"Error en la línea 45"_.|
|`nano +15 archivo.txt`|Abrir en una línea específica (`+`).|Abre el documento y sitúa el cursor de forma directa sobre la línea número 15 (o la que le indiques). Te ahorra tener que hacer _scroll_ hacia abajo en archivos gigantescos.|
|`nano -B archivo.conf`|Crear copia de seguridad (`-B`).|Antes de aplicar tus cambios y guardar, `nano` crea automáticamente un archivo de respaldo con el contenido original terminado en una virgulilla (`archivo.conf~`). Tu red de seguridad si rompes algo.|




## Asignación de Variables y Sintaxis Estricta

Reglas críticas de espaciado y manipulación de variables numéricas dentro del entorno de Bash.

| **Sintaxis Especial** | **Para qué sirve**                | **Explicación del truco**                                                                                                                                                                              |
| --------------------- | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `contador=1`          | Asignación estándar.              | **Sin espacios.** Guarda el valor de la derecha dentro de la variable de la izquierda de forma directa.                                                                                                |
| `$contador`           | Invocar el valor.                 | Para **guardar** un valor no usas el símbolo `$`, pero para **leerlo** o mostrarlo en el `echo`, el `$` es obligatorio.                                                                                |
| `let contador+=1`     | Operación aritmética (`let`).     | Le indica a Bash que lo que viene detrás es una operación matemática. Al igual que la asignación, **no puede llevar espacios** a menos que entrecoches la expresión entera (`let "contador += 1"`).    |
| `((contador++))`      | Alternativa `let`, es más moderno | El doble paréntesis abre un entorno puramente matemático. Aquí **sí te permite usar todos los espacios que quieras** (`(( contador ++ ))`) y es la forma más limpia y recomendada en scripts modernos. |


Ejemplo:
```bash
contador=1
while read line; do
	echo "Linea $contador: $line"
	let contador +=1
done < /etc/passwd #Aqui va el archivo que quieres leer
```

>Las variables se asignan **SIEMPRE SIN ESPACIOS**

>Si lo hacemos en una línea, los saltos de línea debemos de representarlos con `;`




## Manipulación de Datos en `base64`

El formato **Base64** no es un método de cifrado (no oculta información ni requiere contraseñas), sino un sistema de **codificación**. Su objetivo es transformar cualquier tipo de datos (incluyendo archivos binarios, imágenes o caracteres especiales) en un flujo de texto limpio que use únicamente 64 caracteres seguros de la tabla ASCII ($A-Z$, $a-z$, $0-9$, $+$, $/$).

En seguridad y desarrollo, se usa constantemente para transmitir datos a través de canales que solo aceptan texto plano (como cabeceras HTTP, código fuente o payloads).


Codificación y decodificación de flujos de datos e hilos de texto directamente desde la línea de comandos de Linux.

| **Sintaxis Especial**                  | **Para qué sirve**                   | **Explicación del truco**                                                                                                                                                                                      |
| -------------------------------------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `echo -n "texto" \| base64`            | **Codificar texto plano.**           | Convierte una cadena de texto a Base64. El flag **`-n`** en el `echo` es obligatorio para evitar que se añada un salto de línea invisible al final, lo que alteraría el resultado.                             |
| `echo "Y29kaWZpY2Fkbw==" \| base64 -d` | **Decodificar texto plano (`-d`).**  | Toma una cadena en Base64 a través del _pipe_ y la revierte a su texto original legible para humanos.                                                                                                          |
| `base64 archivo.bin > output.txt`      | Codificar un archivo completo.       | Lee un archivo binario (como un ejecutable o una imagen) y genera un archivo de texto plano con todo su contenido transformado en Base64.                                                                      |
| `base64 -d archivo.txt > original.bin` | Decodificar un archivo completo.     | Toma un archivo de texto en Base64 y reconstruye el archivo binario original de forma exacta.                                                                                                                  |
| `base64 -w 0 archivo`                  | Desactivar saltos de línea (`-w 0`). | Por defecto, el comando mete un salto de línea cada 76 caracteres para que sea legible en terminales. Al poner `-w 0`, te devuelve todo el Base64 en una sola línea continua (ideal para pegar en _exploits_). |

### 💡 El truco del "Padding" (Los signos `=` al final)

Cuando codificas en Base64, verás muy a menudo que el resultado termina con uno o dos signos de igual (por ejemplo, `Y29kaWZpY2Fkbw==`).

Esto se conoce como **Padding** (relleno). Base64 procesa los datos en bloques estrictos de 3 bytes (24 bits). Si el texto original que estás intentando codificar no tiene una longitud que sea múltiplo exacto de 3, el comando rellena los huecos vacíos al final con caracteres `=` para que el bloque mida exactamente lo que el algoritmo espera.

> Si ves una cadena extraña en un código que termina en `=`, hay un 99% de probabilidades de que sea un texto en Base64 listo para ser decodificado con `base64 -d`.



## Traductor de Caracteres `tr`

A diferencia de `sed` o `awk`, que trabajan analizando palabras completas o líneas enteras, `tr` funciona como un escáner que va **letra por letra** sustituyendo de forma masiva lo que le indiques. Una regla crucial de `tr` es que **no sabe leer archivos directamente**; siempre necesita recibir el texto a través de una tubería (`|`) o de una redirección (`<`).

Modifica flujos de texto operando a nivel de caracteres individuales para transformar mayúsculas, eliminar elementos repetidos o purgar texto no deseado.

>**Ojo:** `tr` solo sustituye las letras que hay por otras, **NO AÑADE MÁS,** por ejemplo si root lo cambiamos por maria, solo se quedará `mari`, porque solo habia 4 caracteres que sustituir, si queremos sustituir pudiendo añadir mas letras, usaríamos `sed`

| **Sintaxis Especial**       | **Para qué sirve**                    | **Explicación del truco**                                                                                                                                                              |
| --------------------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `echo "hola" \| tr 'o' 'x'` | Sustitución simple de letras.         | Busca todas las letras 'o' del flujo y las cambia instantáneamente por letras 'x'. El resultado será `hxla`.                                                                           |
| `tr 'a-z' 'A-Z' < archivo`  | **Convertir todo a mayúsculas.**      | Define dos rangos. Mapea cada letra minúscula con su equivalente en mayúscula. Es el método más rápido en Linux para normalizar texto.                                                 |
| `tr -d ' ' < archivo`       | Eliminar caracteres (`-d`).           | El flag **`-d`** (_delete_) borra por completo los caracteres indicados. En este ejemplo, elimina absolutamente todos los espacios en blanco del texto.                                |
| `tr -s ' ' < archivo`       | Comprimir duplicados (`-s`).          | El flag **`-s`** (_squeeze_) busca caracteres idénticos que estén seguidos y los comprime en uno solo. Ideal para convertir múltiples espacios en blanco seguidos en un único espacio. |
| `tr -cd '0-9' < archivo`    | Quedarse solo con lo elegido (`-cd`). | El flag **`-c`** (_complement_) invierte la selección. Borra absolutamente todo **excepto** lo que le indiques. Este comando purga el texto y te deja únicamente los números.          |
| `tr '\n' ',' < archivo`     | Cambiar saltos de línea.              | Sustituye cada salto de línea (`\n`) por una coma. Convierte una lista vertical de datos en una sola línea horizontal separada por comas.                                              |

### 💡 El truco del "Cifrado César" en una sola línea

Dado que `tr` mapea caracteres uno a uno de forma estricta, puedes utilizarlo para descifrar o cifrar mensajes usando el algoritmo **ROT13** (un cifrado César clásico que desplaza cada letra 13 posiciones hacia adelante en el abecedario).

Si interceptas un texto cifrado en un CTF, puedes revertirlo rotando los rangos de las letras así:

```bash
echo "pelbgbtersvn" | tr 'a-zA-Z' 'n-za-mN-ZA-M'
```

- **`'a-z'`**: Es el alfabeto original.
    
- **`'n-za-m'`**: Es el alfabeto empezando desde la 'n' y dando la vuelta.(ROT13 = 13 posiciones) `tr` cambiará de forma simétrica la _p_ por la _c_, la _e_ por la _r_, etc., revelando la palabra oculta: `criptografia`.


## Extractor de Columnas y Secciones `cut`

Corta y extrae secciones específicas de texto, caracteres o columnas de cada línea de un archivo o flujo de datos, siendo ideal para procesar líneas formateadas.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`cut -c 1-5 archivo`|Extraer por posición de caracteres (`-c`).|Corta y te muestra únicamente desde el carácter número 1 hasta el 5 de cada línea. Excelente para procesar datos con un ancho fijo estricto.|
|`cut -d ":" -f 1 /etc/passwd`|Extraer columnas con delimitador (`-d`).|Define el carácter separador de columnas mediante `-d` (en este caso, los dos puntos `:`) y extrae la columna deseada con `-f` (_field_). Devuelve la lista limpia de usuarios.|
|`cut -d " " -f 1,3 archivo`|Extraer múltiples columnas específicas.|Al usar la coma `,`, puedes elegir varias columnas separadas (la primera y la tercera). Es el truco clásico para limpiar salidas de comandos espaciadas.|
|`cut -d "," -f 2-4 archivo`|Extraer un rango de columnas continuas.|Utiliza el guion `-` para extraer un bloque completo de columnas (de la segunda a la cuarta, ambas inclusive). Muy útil para procesar archivos CSV.|
|`cut -d " " -f 2- archivo`|Extraer desde una columna hasta el final.|Al dejar el número final vacío después del guion (`2-`), le ordenas a `cut` que extraiga desde la segunda columna hasta que termine la línea completa.|
|`cut -d " " --complement -f 3`|Invertir la selección (`--complement`).|Extrae absolutamente todas las columnas del archivo **excepto** la tercera. Te ahorra tener que escribir todas las demás columnas a mano.|

### 💡 Qué problema tiene `cut` (Y cómo solucionarlo)

`cut` tiene un problema: **no sabe gestionar los espacios en blanco repetidos**. Si un comando te devuelve una tabla donde algunas columnas están separadas por un espacio y otras por tres espacios para alinearse visualmente, `cut` fallará y te devolverá columnas vacías.

**El truco:** Si vas a procesar texto separado por espacios irregulares, primero debes "limpiar" el texto usando `tr -s " "` (que comprime múltiples espacios en uno solo) antes de pasárselo a `cut`, o directamente usar `awk`, el cual ignora los espacios repetidos por defecto:

```bash
# Forma incorrecta (falla si hay espacios extras):
cat tabla.txt | cut -d " " -f 2

# Forma correcta combinando trucos:
cat tabla.txt | tr -s " " | cut -d " " -f 2
```


## Volcado Hexadecimal `xxd`

Transforma archivos binarios en representaciones legibles de base 16 (hexadecimal) y permite realizar la operación inversa para compilar parches o payloads desde texto plano.

El comando **`xxd`** es una de las herramientas de bajo nivel más importantes en hacking, ingeniería inversa y forense digital. Su función principal es **crear un volcado hexadecimal (_hex dump_)** de cualquier archivo o flujo de datos, permitiéndote ver los bytes binarios reales que componen un programa, una imagen o un exploit, junto con su representación en texto plano.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`xxd archivo.bin`|Volcado estándar.|Muestra tres columnas: la dirección de memoria (_offset_), los bytes en hexadecimal y el texto ASCII equivalente (poniendo un punto `.` si el byte no es imprimible).|
|`xxd -r archivo.hex > nuevo.bin`|**Operación inversa (`-r`).**|Reinvierte el proceso (_reverse_). Toma un archivo de texto con código hexadecimal y lo reconstruye de vuelta a un archivo binario ejecutable real.|
|`xxd -p archivo.bin`|Formato plano (_plain_) (`-p`).|**El flag más usado en scripts.** Elimina los offsets y la columna ASCII, devolviendo únicamente la cadena continua de números hexadecimales. Ideal para extraer firmas o hashes.|
|`xxd -l 32 archivo.bin`|Limitar la longitud (`-l`).|Le dice al comando que se detenga tras procesar el número de bytes indicado (en este caso, los primeros 32 bytes). Perfecto para inspeccionar cabeceras de archivos sin saturar la pantalla.|
|`xxd -c 4 archivo.bin`|Modificar columnas (`-c`).|Cambia la cantidad de bytes que se muestran por cada fila. Si pones `-c 4`, organizará el volcado en grupos estrictos de 4 bytes (ideal para visualizar arquitecturas de 32 bits).|
|`xxd -b archivo.bin`|Volcado binario puro (`-b`).|En lugar de mostrar los datos en base 16 (hexadecimal), te muestra los bytes desglosados en bits puros (ceros y unos, base 2).|

### 💡 El truco forense: Identificar un archivo "mutilado" por su cabecera

A veces, un atacante cambia la extensión de un archivo (por ejemplo, renombra un script malicioso `.php` a `.jpg`) para saltarse un sistema de seguridad. Como los primeros bytes de un archivo (conocidos como _Magic Numbers_) nunca mienten, puedes usar `xxd` para descubrir su verdadera identidad:

Bash

```
xxd -l 4 imagen_sospechosa.jpg
```

Si el resultado empieza por `7f45 4c46` (que en ASCII se lee `.ELF`), sabrás al instante que **no es una imagen**, sino un ejecutable de Linux camuflado. Si empieza por `8950 4e47`, sabrás que efectivamente es un archivo `PNG` real.


## Compresor Multiformato `7z`

Herramienta de alta compresión que permite empaquetar, cifrar y extraer archivos en formato `.7z`, así como gestionar extensiones comunes (`.zip`, `.tar`, `.gz`, `.rar`) directamente desde la línea de comandos. En auditorías de seguridad, es clave para exfiltrar datos de forma masiva o analizar _payloads_ comprimidos.

| **Sintaxis Especial**                        | **Para qué sirve**                         | **Explicación del truco**                                                                                                                                                                                                                |
| -------------------------------------------- | ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `7z a archivo.7z carpeta/`                   | **Crear un archivo comprimido (`a`).**     | La bandera `a` (_add_) empaqueta y comprime el directorio indicado dentro de un contenedor `.7z`.                                                                                                                                        |
| `7z x archivo.7z`                            | **Extraer con rutas completas (`x`).**     | La bandera `x` (_eXtract_) descomprime el archivo manteniendo intacta la estructura original de carpetas y subcarpetas. **Nota:** No uses `7z e`, ya que extrae todos los archivos sueltos en la misma carpeta, rompiendo la estructura. |
| `7z l archivo.7z`                            | Listar contenido (`l`).                    | Muestra en pantalla el listado de archivos dentro del comprimido (con tamaños y fechas) sin necesidad de gastar tiempo ni disco en descomprimirlo.                                                                                       |
| `7z a -p"Pass123" -mhe=on datos.7z secreta/` | **Cifrado militar con ocultación (`-p`).** | Aplica cifrado AES-256. El modificador `-mhe=on` (_Header Encryption_) cifra también los nombres de los archivos. Si alguien intenta listar el contenido con `7z l`, le pedirá la contraseña antes de poder ver siquiera los nombres.    |
| `7z t archivo.7z`                            | Verificar integridad (`t`).                | Realiza un test (`t`est) simulando la descompresión para comprobar si el archivo está corrupto o si ha sido modificado.                                                                                                                  |
| `7z x archivo.gzip`                          | `x` (descomprime)                          | Permite descomprimir el archivo.                                                                                                                                                                                                         |

### Descompresión recursiva `7z` con script

>Si un archivo  es comprimido muchas veces a si mismo, podemos automatizar la descompresión mediante esta combinación de comandos.

Partimos de un archivo comprimido `content.gzip` que dentro contiene otro comprimido `data.gzip` continuamente.

1. Obtenemos el nombre del archivo comprimido(el interior)
	1. `7z l content.gzip | grep "Name" -A 2 | tail -n 1 | awk 'NF{print $NF}'`(y lo copiamos para luego copiarlo en el script)
2.  Creamos un script `nano decompresor.sh`
	1. arriba debemos de poner `#!/bin/bash` (Siempre para crear un script)
	2. Creamos una variable `name_compressed=$(7z l content.gzip | grep "Name" -A 2 | tail -n 1 | awk 'NF{print $NF}')
	3. Descomprimimos `7z x content.gzip`
	4. Limpiamos los mensajes de error de la terminal(STDERR), y los mensajes de flujo de la terminal al descomprimir(STDIN), así que añadimos: `7z x content.gzip > /dev/null 2>&1`
	5. Tenemos que establecer cuando acaba de descomprimir, es decir creamos un bucle y acabamos cuando la operación nos devuelva un bit de estado = 1 (fallido, es decir que no lo ha completado correctamente, porque ya no es un comprimido que puede descomprimir). 
	6. El paso 3, 4, 5 queda así:
		```bash
		name_decompressed=$(7z l content.gzip | grep "Name" -A 2 | tail -n 1 | awk 'NF{print $NF}')
		7z x content.gzip > /dev/null 2>&1
		
		while true; do
			7z l $name_decompressed > /dev/null 2>&1
			
			if [ "$(echo$?)" == "0" ]; then # OJO: echo$?, nos da el bit de estado, 0 == exitoso, es decir que a podido ser descomprimido con exito(porque es un comprimido)
				decompressed_next=$(7z l $name_decompressed | grep "Name" -A 2 | tail -n 1 | awk 'NF{print $NF}')
				7z x $name_decompressed > /dev/null 2>&1 && name_decompressed=$decompressed_next
				
			else
				cat$name_decompressed
				exit 1
			fi
		done
		```

Finalmente ejecutamos el script: `./decompressor.sh`
### 💡Comprimir y trocear un archivo gigante para exfiltración

Cuando estás comprometimiento un servidor y necesitas llevarte un archivo de base de datos enorme (por ejemplo, de 10 GB), intentar descargarlo de un tirón puede levantar alertas en los sistemas de detección de anomalías de red (IDS).

Puedes usar `7z` para comprimirlo y, al mismo tiempo, **dividirlo en trozos pequeños de tamaño fijo** (por ejemplo, partes de 100 MB) que puedas descargar de forma paulatina o camuflada:


```bash
7z a -v100m exfiltracion.7z /var/www/backup_gigante/
```

- **`-v100m`**: Activa el volumen dinámico (_volume_). Trocea el archivo final en partes llamadas `exfiltracion.7z.001`, `exfiltracion.7z.002`, etc., cada una de exactamente **100 Megabytes**.
    
- Para volver a unirlos en tu máquina local, simplemente pones todos los trozos en la misma carpeta y ejecutas la extracción normal sobre el primero de ellos: `7z x exfiltracion.7z.001`. El programa detectará el resto de forma automática.


## Protocolo de Conexión Segura `ssh`

Hay información mas detallada sobre ssh y como iniciar sesión en [[SSH (Fundamentos)]]

El comando **`ssh`** (Secure Shell) es la herramienta estándar en Linux para iniciar sesión de forma remota en servidores a través de una red. Funciona cifrando de extremo a extremo todo el tráfico (comandos, contraseñas y transferencias) en el **puerto 22** por defecto, evitando interceptaciones en la red.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`ssh usuario@192.168.1.50`|Conexión estándar.|Abre una consola interactiva remota en la IP indicada usando las credenciales del usuario especificado.|
|`ssh -p 2222 usuario@ip`|Cambiar el puerto por defecto (`-p`).|Te conecta a un servidor que ha movido su servicio SSH (por ejemplo, al puerto 2222) para esquivar escaneos automáticos de bots.|
|`ssh -i id_rsa usuario@ip`|**Autenticación por llave privada (`-i`).**|Inicia sesión de forma criptográfica usando tu archivo de clave privada (`id_rsa`), saltándose la necesidad de escribir contraseñas.|
|`ssh -t usuario@ip "nano /etc/conf"`|**Forzar terminal interactiva (`-t`).**|**Obligatorio** si ejecutas un comando en una sola línea que sea interactivo (como `nano`, `top` o `sudo su`). Obliga a asignar una TTY remota para que responda a tus teclas.|
|`ssh -L 9000:localhost:3306 user@ip`|**Túnel SSH / Port Forwarding (`-L`).**|Trae un puerto oculto del servidor (como una base de datos en el `3306`) hacia tu máquina local (puerto `9000`) de forma segura por dentro del túnel.|

### 💡 Recordatorio de Permisos

Si vas a usar llaves privadas (`-i`), recuerda que SSH es extremadamente estricto con la seguridad en Linux. Si tu archivo de clave tiene permisos demasiado abiertos, el comando fallará por seguridad.

Debes aplicar siempre estos permisos en tu terminal local antes de conectar:

```Bash
chmod 700 ~/.ssh/
chmod 600 ~/.ssh/id_rsa
```



## Buscador de Archivos Abiertos `lsof`

El comando **`lsof`** (abreviatura de _List Open Files_, listar archivos abiertos) es una de las herramientas de diagnóstico más potentes en Linux.

En los sistemas Unix, **todo es un archivo**: un documento de texto, una carpeta, un proceso, una tubería de comunicación, un dispositivo de hardware o incluso una conexión de red (un _socket_). Por lo tanto, `lsof` te permite ver con precisión milimétrica qué programas o procesos del sistema están utilizando qué recursos en cada momento.

Rastrea, filtra y monitoriza en tiempo real qué procesos del sistema tienen abiertos determinados archivos, carpetas, puertos de red o recursos de hardware.

| **Sintaxis Especial**       | **Para qué sirve**                          | **Explicación del truco**                                                                                                                                             |
| --------------------------- | ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lsof -i`                   | **Listar conexiones de red (`-i`).**        | **El flag rey en seguridad.** Muestra todos los sockets de red abiertos (puertos e IPs) por los programas del sistema, desglosando si usan IPv4 o IPv6.               |
| `lsof /var/log/auth.log`    | Ver quién usa un archivo.                   | Te muestra qué proceso, usuario y PID está leyendo o escribiendo en ese archivo concreto en este preciso instante.                                                    |
| `lsof +D /var/www/html/`    | **Escanear un directorio completo (`+D`).** | Escanea la carpeta indicada y todas sus subcarpetas para decirte qué procesos tienen bloqueados esos archivos. Vital para saber por qué no puedes desmontar un disco. |
| `lsof -u apache`            | Filtrar por usuario (`-u`).                 | Muestra absolutamente todos los archivos y recursos que tiene abiertos el usuario especificado (en este caso, el servidor web).                                       |
| `lsof -p 1234`              | Filtrar por PID (`-p`).                     | Pone bajo el microscopio a un proceso concreto usando su ID. Te desglosa todo lo que ese programa está tocando en el sistema.                                         |
| `lsof -i :80`               | Filtrar por puerto específico.              | Te dice qué programa exacto está escuchando o utilizando un puerto (el 80 en este ejemplo). Ideal para solucionar el típico error de: _"Port already in use"_.        |
| `lsof -i TCP -s TCP:LISTEN` | Buscar puertos en escucha.                  | Filtra la red para mostrarte únicamente los servicios que tienen un puerto abierto esperando conexiones entrantes (_backdoors_, servidores web, SSH, etc.).           |

### 💡`lsof` + `kill` (Forzar liberación)

Seguro que te ha pasado: intentas borrar una carpeta o desmontar un pendrive (`umount /media/usb`) y Linux te escupe el odiado error: _`target is busy`_ (el objetivo está ocupado).

En lugar de volverte loco adivinando qué programa se ha quedado colgado tocando el disco, puedes usar el flag **`-t`** (_terse_) de `lsof`. Este flag limpia toda la salida de la pantalla y te devuelve **únicamente el número de PID** del culpable, permitiéndote aniquilarlo en una sola línea de comandos:

Bash

```
kill -9 $(lsof -t /media/usb)
```

- **`lsof -t /media/usb`**: Encuentra el archivo abierto en esa ruta y extrae solo su PID (por ejemplo, `4521`).
    
- **`kill -9`**: Recibe ese PID y fulmina el proceso de forma inmediata, liberando el recurso al instante.



## Rastreador de Directorios de Procesos `pwdx`

Identifica el directorio de trabajo actual (_Current Working Directory_) de uno o varios procesos activos en el sistema basándose en su identificador numérico (PID).

El comando **`pwdx`** es una herramienta de diagnóstico ultra específica y rápida. Su nombre viene de combinar **`pwd`** (_Print Working Directory_) y la **`x`** de _proceso_.

Le pasas el número de **PID** (ID de proceso) de un programa que se esté ejecutando en el sistema, y te dice **en qué carpeta exacta está trabajando ese proceso**.

En análisis forense y respuesta ante incidentes, es un comando letal. Si ves un proceso sospechoso corriendo en el fondo con un nombre raro, `pwdx` te dirá inmediatamente desde qué directorio oculto se ha ejecutado.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`pwdx 1234`|Consulta estándar.|Muestra el PID seguido de la ruta absoluta de la carpeta donde está operando ese programa.|
|`pwdx 1234 5678 9101`|Consulta múltiple.|Puedes pasarle varios PIDs separados por espacios en la misma línea y te listará las rutas de todos ellos del tirón.|

### 💡 El combo de Caza de Malware: `ps` + `pwdx`

Cuando un servidor sufre un hackeo, los atacantes suelen camuflar sus virus poniéndoles nombres de procesos legítimos del sistema (como `nginx`, `apache` o `syslogd`) para que pasen desapercibidos en el Administrador de Tareas.

Sin embargo, un proceso legítimo de `nginx` debería estar corriendo en carpetas del sistema (como `/usr/sbin/`). Si usas la sustitución de comandos de Bash para pasarle a `pwdx` los PIDs de esos procesos sospechosos, puedes cazar impostores al vuelo:

```Bash
pwdx $(pgrep nginx)
```

- **`pgrep nginx`**: Busca todos los procesos que se llamen "nginx" y extrae solo sus números de PID.
    
- **`pwdx $(...)`**: Recibe esos PIDs y te escupe sus rutas de trabajo.
    

> ⚠️ **El truco del analista:** Si el resultado de este comando te dice que un proceso llamado `nginx` está corriendo dentro de `/tmp/` o `/var/tmp/`, felicidades: acabas de cazar un _script_ malicioso o un _backdoor_ intentando camuflarse.



## Visor de Procesos Activos `ps`

El comando **`ps`** (_Process Status_) toma una captura estática ("foto") de los procesos que se están ejecutando en el sistema en un instante preciso. A diferencia de `top` o `htop`, que consumen recursos monitorizando en tiempo real, `ps` se ejecuta una sola vez y muere, siendo la herramienta idónea para filtrar, analizar y automatizar scripts de administración o forense.

Aquí tienes la tabla de referencia con los modificadores más potentes adaptada estrictamente a tu formato de notas:

| **Sintaxis Especial** | **Para qué sirve**                          | **Explicación del truco**                                                                                                                                                                                                                                                                     |
| --------------------- | ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ps`                  | Consulta básica.                            | Muestra únicamente los procesos activos que pertenecen al usuario actual y que se iniciaron en la misma terminal (_TTY_) desde la que lanzas el comando.                                                                                                                                      |
| `ps -eo command`      | **Muestra solo los procesos  que quieras**  | `-e`, muestra todos los procesos. `-o command`, filtra por comandos que se ejecuten a nivel del sistema.                                                                                                                                                                                      |
| `ps aux`              | **Ver absolutamente todo (Estilo BSD).**    | **El clásico imprescindible.**<br><br>  <br><br>• `a`: Muestra procesos de todos los usuarios.<br><br>  <br><br>• `u`: Añade columnas detalladas (usuario, %CPU, %Memoria).<br><br>  <br><br>• `x`: Incluye procesos que no tienen una terminal asociada (servicios del sistema o _daemons_). |
| `ps -ef`              | Ver absolutamente todo (Estilo System V).   | Alternativa estándar a `aux`. El flag `-e` muestra todos los procesos y `-f` genera un listado de formato completo (_full_), detallando el **PPID** (ID del proceso padre) y los comandos con sus argumentos exactos.                                                                         |
| `ps -u apache`        | Filtrar por usuario (`-u`).                 | Muestra de golpe todos los procesos que pertenecen a un usuario específico (en este caso, el servidor web Apache).                                                                                                                                                                            |
| `ps -C nginx`         | Filtrar por nombre exacto (`-C`).           | Busca únicamente los procesos cuyo comando coincida de forma exacta con la palabra indicada (_Command_), ahorrándote usar `grep`.                                                                                                                                                             |
| `ps axjf`             | **Mostrar árbol de procesos (`-j` o `f`).** | Dibuja un esquema visual con líneas tipográficas que muestra la jerarquía de los procesos. Te permite ver con total claridad qué proceso "padre" ha engendrado a qué proceso "hijo".                                                                                                          |



## Escaneo de Puertos Nativo con `/dev/tcp`

Aprovecha una característica integrada en Bash que permite abrir conexiones TCP directas como si fuesen archivos, ideal para auditar puertos en entornos restringidos donde no hay `nmap`, `nc` ni `telnet`.

| **Sintaxis Especial**                                   | **Para qué sirve**                 | **Explicación del truco**                                                                                                                                           |
| ------------------------------------------------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `echo ' ' > /dev/tcp/127.0.0.1/30000`                   | Lanzar la sonda al puerto.         | Intenta inyectar un espacio vacío en el puerto 30000. Si el puerto está abierto, el sistema acepta el byte y el comando termina en silencio.                        |
| `echo $?`                                               | **Comprobar el resultado (`$?`).** | **`0` = Éxito (Puerto Abierto).**<br><br>  <br><br>**`1` = Fallo (Puerto Cerrado).** Es la forma de validar el estado de forma inmediata.                           |
| `timeout 1 bash -c "echo > /dev/tcp/10.10.10.10/30000"` | Evitar que se quede colgado.       | Si el puerto está protegido por un Firewall, la terminal se quedará congelada esperando. Usar `timeout 1` obliga a abortar la conexión si no responde en 1 segundo. |


## Herramienta de Red Multiusos `nc` (Netcat)

Conocida como la "navaja suiza" de las redes, **`nc`** permite abrir conexiones TCP/UDP, escuchar en puertos específicos, transferir archivos e incluso improvisar escaneos de puertos de forma mucho más rápida y flexible que el método nativo de `/dev/tcp`.

| Sintaxis Especial              | Para qué sirve                          | Explicación del truco                                                                                                                                                                                                           |
| ------------------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nc 127.0.0.1 30000`           | **Conexión interactiva pura**           | Te conecta al puerto de forma abierta. Todo lo que escribas a partir de ese momento en tu terminal se enviará directamente al servicio que esté corriendo en ese puerto                                                         |
| `nc -zv 127.0.0.1 30000`       | **Escaneo de un puerto básico.**        | El flag **`-z`** (_zero-I/O_) le dice a Netcat que solo verifique si el puerto está abierto, sin enviar datos. El flag **`-v`** (_verbose_) te escupe el resultado explícito en pantalla (_Connection refused_ o _Succeeded!_). |
| `nc -zv 10.10.10.1 20-80`      | Escaneo de rangos de puertos.           | Escanea de golpe todos los puertos comprendidos entre el 20 y el 80 en la IP objetivo.                                                                                                                                          |
| `nc -w 1 -zv 10.10.10.1 30000` | **Establecer un tiempo límite (`-w`).** | El flag **`-w 1`** (_timeout_) le dice que si no recibe respuesta en 1 segundo, aborte la conexión. Vital para que el escaneo no se quede congelado si el puerto está protegido por un firewall.                                |
| `nc -l -p 4444`                | Levantar un puerto en escucha (`-l`).   | Convierte tu máquina en un servidor temporal que escucha (_listen_) en el puerto (`-p`) 4444 esperando conexiones entrantes.                                                                                                    |
| `-nc -n`                       | `-n` (No resuelve DNS)                  | Mas rapidez                                                                                                                                                                                                                     |


>Podemos enviar texto mediante la salida de `echo "texto a enviar" | nc 127.0.0.1 30000`, también otra idea seria enviar un diccionario de claves`cat dictionary.txt | nc 178.23.9.7 30001`

## Herramienta de Diagnóstico de Red `telnet`

Aunque originalmente se diseñó como un protocolo para administrar servidores remotos, hoy en día está **totalmente en desuso para ese fin** porque viaja en texto plano (sin cifrar) y expone tus contraseñas. Sin embargo, en el mundo del _networking_ y la ciberseguridad, el comando **`telnet`** sigue siendo un aliado espectacular para **comprobar la conectividad de puertos TCP** y para interactuar manualmente con servicios de red (como servidores de correo SMTP o servidores web HTTP).

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`telnet 127.0.0.1 30000`|**Comprobar si el puerto está abierto.**|Intenta conectar a la IP y puerto indicados. Si se queda en _`Trying...`_, está cerrado o filtrado. Si dice _`Connected`_, el puerto está **ABIERTO** y esperando órdenes.|
|`ctrl + ]` y luego `quit`|**Cerrar una sesión colgada.**|El mayor dolor de cabeza de `telnet` es salir de él cuando te conectas a un puerto que no responde. Usas la combinación `Ctrl + ]` para entrar en la consola de comandos de telnet, y luego escribes `quit` para forzar la salida.|


## SSL `openssl`

SSL Se explica en [[Arquitectura PKI y Protocolo TLS]]
La herramienta estándar en Linux para gestionar, interactuar y verificar certificados SSL/TLS desde la terminal es **`openssl`**.

| **Sintaxis Especial**                      | **Para qué sirve**                     | **Explicación del truco**                                                                                                                                                 |
| ------------------------------------------ | -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `openssl s_client -connect google.com:443` | **Simular un navegador web.**          | Se conecta al puerto 443 simulando el handshake. Te escupe en la terminal toda la cadena de certificados, el cifrado que está usando y si la conexión es segura.          |
| `openssl x509 -in cert.crt -text -noout`   | **Inspeccionar un certificado local.** | El flag `-text` traduce el archivo binario del certificado (`.crt` o `.pem`) a texto legible para humanos, mostrando su fecha de expiración, el dominio y quién lo firmó. |
| `openssl genrsa -out privada.key 2048`     | Generar una llave privada RSA.         | Genera una clave privada asimétrica de 2048 bits de longitud. Es el primer paso obligado antes de crear cualquier certificado propio.                                     |

## Listar puertos abiertos `nmap`
[[NMAP]]


EL uso de la flag -T5 es MUY ARRIESGADO, solo usarlo en entornos controlados.

| **Sintaxis Especial**                          | **Para qué sirve**                      | **Explicación del truco**                                               |
| ---------------------------------------------- | --------------------------------------- | ----------------------------------------------------------------------- |
| `nmap --open`                                  | `--open` (Listar puertos abiertos)      |                                                                         |
| `nmap --open -T5`                              | `-T5` (Modo más agresivo)               | IMPORTANTE, solo para uso en local [[NMAP]]                             |
| `nmap --open -T5 -v`                           | `-v` (Verbose)                          | A medida que va encontrando puertos, los va poniendo en la consola      |
| `-nmap --open -T5 -v -n`                       | `-n` (Sin resolución DNS)               | No resuelve el DNS, es decir NO hace esto, 189.44.23.223 -> Example.com |
| `-nmap --open -T5 -v -n -p30000-32000`         | `-p`(Filtrar puertos en los que actuar) | Solo hace la busqueda de los puertos entre el 30000 y el 32000          |
| `-nmap --open -T5 -v -n -p320-9000 184.0.56.2` | **Indicar siempre la IP**               | **Indicar siempre la ip en la que actuar**                              |

## Crear Archivos Temporales `mktemp`

El comando **`mktemp`** (Make Temporary) sirve para **crear archivos o directorios temporales de forma única y segura**.

En lugar de inventarte un nombre como `/tmp/mi_script.txt` (lo cual es una pésima práctica de seguridad porque otro usuario o proceso podría adivinarlo, leerlo o sobreescribirlo), `mktemp` genera un nombre completamente aleatorio basándose en una plantilla, crea el recurso en el disco y te devuelve la ruta exacta. Por defecto, los crea dentro de la carpeta `/tmp/`, la cual se vacía automáticamente cada vez que se reinicia el sistema.


|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`mktemp`|Creación básica estándar.|Crea un archivo vacío en `/tmp/` con un nombre aleatorio (ej: `/tmp/tmp.XceR4vP91a`) y te muestra la ruta en pantalla.|
|`mktemp -d`|**Crear un directorio temporal (`-d`).**|En lugar de un archivo suelto, crea una carpeta temporal vacía. Es el modificador ideal si tu script va a descargar o generar múltiples archivos intermedios.|
|`mktemp -u`|Simular sin crear (`-u`).|Modo _unsafe_ o _dry-run_. Te muestra en pantalla el nombre aleatorio que generaría, pero **no crea el archivo en el disco**. Útil si solo quieres la cadena de texto aleatoria.|


## Comparador de Archivos `diff`

El comando **`diff`** (Difference) analiza y compara línea por línea el contenido de dos archivos de texto (o incluso el contenido de dos directorios). Te muestra detalladamente qué líneas son distintas, cuáles se han borrado y cuáles se han añadido, siendo la herramienta base sobre la que funcionan los sistemas de control de versiones como Git.

| **Sintaxis Especial**                         | **Para qué sirve**                              | **Explicación del truco**                                                                                                                                                                                                             |
| --------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `diff archivo1.txt archivo2.txt`              | Comparación básica estándar.                    | Muestra las diferencias usando un formato crudo clásico (`a` para _add_, `d` para _delete_, `c` para _change_).                                                                                                                       |
| `diff -u archivo1.txt archivo2.txt`           | **Formato unificado (`-u`).**                   | **El formato más legible.** Muestra el contenido combinado usando símbolos de colores: `+` en verde para las líneas añadidas en el segundo archivo y `-` en rojo para las líneas borradas. Es el formato estándar que usa `git diff`. |
| `diff -w archivo1.txt archivo2.txt`           | Ignorar espacios en blanco (`-w`).              | Compara los archivos ignorando por completo tabuladores o espacios en blanco repetidos. Útil en programación para ver si el código real cambió sin importar la indentación.                                                           |
| `diff -i archivo1.txt archivo2.txt`           | Ignorar mayúsculas (`-i`).                      | Hace que la comparación sea insensible a mayúsculas y minúsculas (_case-insensitive_).                                                                                                                                                |
| `diff -r carpeta1/ carpeta2/`                 | **Comparar directorios completos (`-r`).**      | Modo recursivo. Compara los nombres de los archivos dentro de ambas carpetas y, si un archivo coincide en nombre en ambos lados, analiza también sus diferencias internas.                                                            |
| `diff -y --suppress-common-lines a.txt b.txt` | Comparar a doble columna ocultando lo idéntico. | El flag `-y` pone los archivos en paralelo (pantalla dividida). Al sumarle `--suppress-common-lines`, esconde todo lo que sea igual y **te muestra únicamente las líneas en conflicto**.                                              |

## Terminal Bash `bash`

Es la terminal.

| **Sintaxis Especial** | **Para qué sirve**                              | **Explicación del truco**                                                                                                                                                                                                  |
| --------------------- | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `bash --norc`         | **Ignorar configuraciones locales (`--norc`).** | Fuerza a Bash a arrancar en modo interactivo pero **sin leer el archivo `~/.bashrc`**. Como vimos antes, es el truco maestro para saltarse scripts trampa de deslogueo automático.                                         |
| `bash -p`             | Para ejecutar un archivo con SUID               | Por defecto si utilizamos bash para abrir un SUID, no nos dejara coger los permisos del usuario de ese archivo por seguridad. Así que usamos la flag `-p`                                                                  |
| `bash --noprofile`    | Ignorar configuraciones globales.               | Evita que una shell de tipo _Login_ lea los archivos `/etc/profile` o `~/.bash_profile`. Útil si el entorno de un servidor está corrupto y quieres entrar limpio.                                                          |
| `bash -c "comando"`   | Ejecutar un _string_ al vuelo.                  | Pasa un comando directamente como texto para que Bash lo ejecute e inmediatamente cierre la sesión. Muy usado en tareas cronificadas (_cronjobs_).                                                                         |
| `bash -x script.sh`   | **Modo Depuración (_Debug_).**                  | **El mejor amigo del programador.** Ejecuta el script mostrando en la pantalla cada línea de código exactamente antes de que se ejecute, sustituyendo las variables por sus valores reales para cazar errores visualmente. |



## Terminal antigual sh `sh`

Es el padre de bash.

>Con sh podemos ejecutar un archivo SUID directamente sin necesidad de flags.

| **Sintaxis Especial** | **Para qué sirve**                      | **Explicación del truco**                                                                                                                                            |
| --------------------- | --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sh script.sh`        | Ejecución estándar compatible.          | Fuerza al sistema a procesar el archivo usando la sintaxis estricta de la Bourne Shell, ignorando cualquier configuración o alias que tengas en tu `.bashrc`.        |
| `sh -c "echo 'Hola'"` | **Ejecutar comandos en cadena (`-c`).** | Pasa una cadena de texto (_string_) para que `sh` la evalúe y la ejecute inmediatamente como un comando. Es el flag más explotado para lanzar payloads remotos.      |
| `#!/bin/sh`           | **El Shebang de portabilidad.**         | **Obligatorio** colocarlo en la primera línea de tus scripts si quieres asegurar que el script funcione de forma idéntica en cualquier distribución Linux del mundo. |

## Generador de Hashes `md5sum`

Calcula y verifica una huella digital única de 128 bits (hash MD5) a partir de un archivo o flujo de texto para comprobar la integridad de los datos.

| **Sintaxis Especial**           | **Para qué sirve**                           | **Explicación del truco**                                                                                                                                                                          |
| ------------------------------- | -------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `md5sum archivo.iso`            | Calcular el hash de un archivo.              | Lee todo el archivo y te devuelve una cadena única de 32 caracteres hexadecimales. Si un solo bit cambia en el archivo original, el hash resultante será completamente diferente.                  |
| `echo -n "texto" \| md5sum`     | Calcular el hash de un texto.                | El truco está en usar **`echo -n`**. La bandera `-n` evita que se añada un salto de línea invisible al final del texto. Si no la pones, el hash cambiará por completo y no coincidirá con el real. |
| `md5sum *.txt`                  | Calcular hashes de forma masiva.             | Genera la huella digital de todos los archivos que terminen en `.txt` dentro de la carpeta actual, mostrándolos en una lista detallada.                                                            |
| `md5sum archivo.iso > hash.md5` | Guardar el hash en un archivo.               | Vuelca el resultado del cálculo dentro de un archivo de texto con extensión `.md5`. Esto es lo que se comparte en internet para que otros verifiquen la descarga.                                  |
| `md5sum -c hash.md5`            | Verificar integridad automáticamente (`-c`). | Compara el hash guardado en el archivo `.md5` con el archivo real que tienes en el disco. Si coinciden, te devolverá un mensaje de **OK** verde y limpio.                                          |

### 💡 El truco del Hacking Ético: ¿Por qué MD5 ya no es seguro para contraseñas?

En seguridad informática y CTFs te vas a encontrar MD5 constantemente, pero debes saber que **ya no se usa para proteger contraseñas** porque está roto criptográficamente.

- **Colisiones:** Es posible alterar un archivo para que genere el mismo hash que otro legítimo (colisión).
    
- **Velocidad:** Como MD5 es ultra rápido de calcular, un atacante puede probar miles de millones de combinaciones por segundo usando la tarjeta gráfica (GPU) mediante herramientas de fuerza bruta como `hashcat`.
    

```Bash
# 1. Guardas el hash oficial que te da la web
echo "b026324c6904b2a9cb4b88d6d61c81d1  kali-linux.iso" > check.md5

# 2. Le dices a tu máquina que lo verifique
md5sum -c check.md5
```



## Monitorización en Tiempo Real `watch`

Ejecuta comandos de forma cíclica e intervalos regulares, refrescando la pantalla por completo para observar cambios en vivo en la salida de los datos.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`watch ls -l`|Monitorizar una carpeta.|Ejecuta `ls -l` cada 2 segundos. El truco ideal para ver en vivo cómo se va descargando un archivo grande o cuándo aparece un archivo nuevo en el sistema.|
|`watch -n 1 comando`|Cambiar el intervalo de tiempo (`-n`).|Modifica los segundos de espera entre cada ejecución (**n**etwork/interval). Con `-n 1` el comando se actualizará cada segundo. El mínimo permitido es `0.1` (diez veces por segundo).|
|`watch -d comando`|Resaltar las diferencias (`-d`).|Activa el modo **d**iferencias. Pinta de color negro o resalta en la pantalla los caracteres exactos que han cambiado respecto a la ejecución anterior. Brutal para cazar cambios sutiles.|
|`watch -t comando`|Ocultar la cabecera (`-t`).|Elimina la barra superior donde `watch` muestra la hora del sistema y el intervalo de segundos, dejándote la pantalla completamente limpia solo con la salida del comando.|
|`watch -g comando`|Salir automáticamente cuando cambie (`-g`).|El truco del **g**oodbye. `watch` se queda ejecutando en bucle hasta que detecta un cambio en la salida del texto; en ese preciso instante, el comando se detiene solo y te devuelve el control de la terminal.|



```Bash
# 1. Vigilar si la máquina víctima nos ha devuelto una conexión por puertos (Netcat)
watch -n 1 netstat -ant

# 2. Monitorizar el espacio en disco mientras descomprimes un backup enorme
watch -d df -h

# 3. Esperar en segundo plano a que un servidor caído responda al ping y que watch se cierre solo cuando responda
watch -g ping -c 1 10.10.10.15
```

_(Nota: Para salir de cualquier bucle de `watch` normal y volver a tu terminal, simplemente pulsa **`Ctrl + C`**)._


## Visor Paginado de Archivos `more`

Permite leer el contenido de archivos de texto largos o salidas de comandos extensas de forma paginada, mostrando una pantalla a la herramienta cada vez y permitiendo avanzar de forma controlada.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`more archivo.txt`|Lectura básica paginada.|Abre el archivo y se detiene al llenar la pantalla. Muestra un porcentaje abajo (`--Más-- (15%)`) indicando cuánto te falta por leer.|
|`ls -la /usr/bin \| more`|Paginación de comandos largos.|El uso rey de `more`. Filtra la salida de un comando masivo a través de una tubería (`\|`) para que no pase a toda velocidad por la terminal y puedas leerla línea a línea.|
|`more +10 archivo.txt`|Empezar a leer desde una línea (`+N`).|Se salta el principio del documento y empieza a mostrar el contenido **directamente a partir de la línea 10**.|
|`more +/"error" log.txt`|Buscar un texto al abrir (`+/`).|Busca la palabra "error" dentro del archivo y abre el visor directamente en la primera línea donde encuentra esa coincidencia.|

### 💡 Atajos de teclado esenciales dentro de `more`

Cuando estás dentro del visor de `more`, la terminal se queda esperando tus órdenes. Estos son los botones que debes pulsar para moverte:

- **`Espacio`**: Avanza una pantalla completa hacia abajo.
    
- **`Enter`**: Avanza línea por línea (para leer con calma).
    
- **`b`**: Retrocede una pantalla completa (_back_). _(Nota: Esto solo funciona con archivos físicos, no con tuberías `\|`)_.
    
- **`q`**: Sale inmediatamente del visor y te devuelve a la consola (_quit_).

>Podemos escapar de un archivo que ejecute un `more` si la pantalla es lo suficiente pequeña para que el contenido no quepa en la pantalla, obligando a mantener more, en este punto pulsamos `v` para abrir el editor de texto, e introducimos el siguiente comando.
#### Escapar del `more` creando una `bash`
```bash 
:set shell=/bin/bash   #La variable shell va a valer /bin/bash
:shell #Ejecutamos la variable shell y ya tenemos una terminal bash
```




## Control de Versiones `git`

Gestiona el historial de cambios de un proyecto, permitiendo coordinar el trabajo en equipo, ramificar código y revertir estados previos de los archivos.

| **Sintaxis Especial**                      | **Para qué sirve**                      | **Explicación del truco (Perspectiva de Hacking/Admin)**                                                                                                                             |
| ------------------------------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `git init`                                 | Inicializar un repositorio local.       | Convierte la carpeta actual en un repositorio de Git. Crea una carpeta oculta `.git` donde se guardará todo el historial de cambios en secreto.                                      |
| `git clone [URL]`                          | Clonar un repositorio remoto.           | El comando rey para descargar herramientas de GitHub. Descarga el proyecto completo junto con todo su historial de versiones a tu máquina.                                           |
| `git log -p archivo`                       | Ver el historial de las versiones       | Ves la diferencias entre los distintos commits.                                                                                                                                      |
| `git status`                               | Ver el estado del proyecto.             | Te dice qué archivos has modificado, cuáles vas a incluir en la próxima foto (_commit_) y cuáles no está rastreando Git todavía.                                                     |
| `git add archivo.py`                       | Preparar archivos (`Staging Area`).     | Mueve el archivo a la "zona de preparación". Es como decirle a Git: _"Pon este archivo frente a la cámara, que le voy a hacer una foto"_. Usar `git add .` añade todo lo modificado. |
| `git commit -m "Mensaje"`                  | Hacer la instantánea (_Commit_).        | Guarda los cambios de forma definitiva en tu historial local con un mensaje explicativo. Cada commit genera un hash único para poder volver a él en el futuro.                       |
| `git log --oneline`                        | Ver el historial simplificado.          | Muestra la lista de todos los commits que se han hecho en el proyecto de forma ultra compacta (un hash corto y su mensaje por línea).                                                |
| `git branch`                               | Gestionar ramas del proyecto.           | Muestra las ramas de desarrollo existentes. Al usar `git branch [nombre]` creas un "universo paralelo" para probar código sin romper la rama principal (_main_).                     |
| `git checkout [rama]`                      | Cambiar de rama o commit.               | Te mueve físicamente a otra rama o te permite viajar en el tiempo a un commit antiguo escribiendo su hash (ej: `git checkout a1b2c3d`).                                              |
| `git push origin main`                     | Subir los cambios a la nube.            | Envía tus commits locales al servidor remoto (como GitHub o GitLab) para que tu equipo se los pueda descargar.                                                                       |
| `git pull`                                 | Descargar y fusionar cambios.           | Trae las últimas actualizaciones que hayan subido tus compañeros desde el repositorio remoto y las fusiona directamente con tu código local.                                         |
| `git branch -r`                            | Listar ramas remotas (`-r`emote).       | Muestra las ramas del servidor (ej: `origin/main`, `origin/feature-auth`) que tu máquina conoce. No muestra tus ramas locales.                                                       |
| `git branch -a`                            | Listar absolutamente todas (`-a`ll).    | Combina en una sola lista las ramas locales (en blanco/verde) y las remotas (en rojo). Ideal para tener una radiografía completa del proyecto.                                       |
| `git branch -r -v`                         | Ramas remotas con detalle (`-v`erbose). | Muestra las ramas remotas junto con el **hash corto y el mensaje del último commit** subido a cada una de ellas sin necesidad de moverte de rama.                                    |
| `git checkout -b [nombre] origin/[nombre]` | Clonar y saltar a una rama remota.      | El truco para empezar a trabajar en una rama que creó un compañero: descarga la rama remota y te crea una copia local idéntica para ti.                                              |
| `git tag`                                  | Vemos los tags                          | Vemos los tags que se haya podido poner, y los abrimos con `git show nombretag`                                                                                                      |
| `git show nombredeltag`                    | Abrir información del tag               | Podemos ver que información guarda el tag                                                                                                                                            |





### 💡La carpeta `.git` expuesta

Cuando un desarrollador sube una página web a un servidor de producción (como Apache o Nginx), a veces comete el gravísimo error de subir también la carpeta oculta **`.git`** a la ruta pública del servidor web (por ejemplo, en `https://empresa.com/.git/`).

**El truco del atacante:** Si encuentras esa carpeta expuesta en una auditoría, puedes usar herramientas como `git-dumper` para descargarte de forma automatizada todo el historial del código fuente de la empresa. Incluso si el programador borró contraseñas o tokens de las bases de datos en la última versión, tú podrás hacer un `git log` y un `git checkout` a los commits antiguos para **robar las credenciales que se quedaron grabadas en el pasado**.




## Variable de Entorno Base `$0`

Variable interna del sistema que almacena la ruta de invocación del script actual o, en su defecto, el identificador del entorno de comandos (shell) activo.

En Bash y Zsh, **`$0`** es una variable especial interna de la terminal que representa el **nombre del script actual o el nombre del shell que se está ejecutando en este preciso momento**.

Es el "argumento cero" de cualquier comando. Mientras que `$1`, `$2` o `$3` representan los parámetros que tú le pasas a un script (los datos que escribes detrás), `$0` se guarda a sí mismo, es decir, el comando que invocó la ejecución.


| **Sintaxis Especial**           | **Qué devuelve**                               | **Explicación del truco**                                                                                                                                                                                                       |
| ------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `$0` (en una terminal normal)   | Abre una nueva terminal hija.                  | Si estás en `bash`, escribir `$0` equivale a escribir `bash`. Te abre un nuevo prompt limpio. Si escribes `exit`, no se cerrará la ventana; simplemente volverás a la sesión anterior.                                          |
| `sudo $0`                       | Te da una shell de `root`.                     | **El truco de velocidad definitivo.** Si necesitas privilegios de administrador y estás en `zsh`, escribir `sudo $0` equivale a `sudo zsh`. Te da una consola de root al instante sin tener que escribir `sudo -i` o `sudo su`. |
| `$0` (dentro de un script)      | Bucle infinito (Fork Bomb).                    | **Peligroso.** Si pones `$0` suelto dentro de un script, cuando lo ejecutes, el script se llamará a sí mismo una y otra vez en un bucle sin fin, lo que puede congelar el sistema por falta de memoria RAM.                     |
| `echo $0` (Directo en terminal) | El nombre del shell (ej: `bash` o `zsh`).      | Si lo ejecutas directamente en la consola, te dice qué intérprete estás usando. Útil para saber en qué entorno estás tras saltar entre contenedores o servidores.                                                               |
| `echo $0` (Dentro de un script) | La ruta del propio script (ej: `./script.sh`). | El truco estándar de automatización. El script sabe cómo se llama a sí mismo, permitiéndole mostrar mensajes de ayuda dinámicos si el usuario lo ejecuta mal.                                                                   |
| `dirname $0`                    | La carpeta donde está el script.               | Extrae la ruta del directorio borrando el nombre del script. Sirve para que un script localice otros archivos de su misma carpeta sin importar desde dónde lo ejecutes.                                                         |
