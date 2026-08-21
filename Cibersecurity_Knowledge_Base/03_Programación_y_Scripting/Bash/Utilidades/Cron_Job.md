Una **tarea Cron** (correctamente escrito **Cron job** o tarea _cron_) es un temporizador automatizado en sistemas basados en Linux y Unix. Sirve para programar la ejecución automática de scripts, comandos o tareas en una fecha y hora exactas, o de forma cíclica (por ejemplo: cada hora, todos los lunes, o una vez al mes).

El nombre proviene del griego _Chronos_ (tiempo), y en el fondo es un servicio en segundo plano (un demonio llamado `crond`) que se despierta cada minuto, revisa un archivo de configuración llamado **`crontab`**, y comprueba si hay alguna tarea programada para ese momento exacto.

En hacking ético y administración, se usan constantemente para programar copias de seguridad, limpiar _logs_, o (desde el punto de vista de un atacante) para asegurar la **persistencia** en un sistema comprometido ejecutando una _reverse shell_ de forma periódica.

## Programación de Tareas `cron`

Administra el programador de tareas del sistema para automatizar la ejecución de comandos en intervalos de tiempo específicos.

|**Sintaxis Especial**|**Para qué sirve**|**Explicación del truco**|
|---|---|---|
|`crontab -l`|Listar tareas (`-l`ist).|Muestra en pantalla todas las tareas cron que tienes programadas actualmente con tu usuario. Clave en auditorías para buscar scripts sospechosos.|
|`crontab -e`|Editar tareas (`-e`dit).|Abre tu archivo `crontab` en el editor de texto de la terminal (como `nano` o `vi`) para que puedas añadir, modificar o borrar programaciones.|
|`crontab -r`|Borrar tareas (`-r`emove).|**Peligroso.** Borra por completo y de un solo golpe todo tu archivo de tareas programadas sin pedir confirmación en muchas versiones.|
|`crontab -u usuario -l`|Ver el cron de otro usuario.|Requiere privilegios de `root`. Te permite listar (`-l`) o editar (`-e`) las tareas programadas de cualquier otro usuario del sistema (ej: `root`, `www-data`).|

### 💡 Explicación de la sintaxis de las 5 estrellas `* * * * *`

Cuando editas un `crontab`, cada línea sigue una estructura estricta de **5 columnas de tiempo** seguidas del comando que quieres ejecutar. Se lee así de izquierda a derecha:

```Plaintext
.---------------- minuto (0 - 59)
|  .------------- hora (0 - 23)
|  |  .---------- día del mes (1 - 31)
|  |  |  .------- mes (1 - 12)
|  |  |  |  .---- día de la semana (0 - 6) (Domingo = 0 o 7)
|  |  |  |  |
*  *  *  *  * /ruta/del/comando_o_script.sh
```

Aquí tienes los trucos de programación más utilizados:

- **`* * * * *` (Cinco estrellas):** El comando se ejecuta **cada minuto** de cada hora de cada día. Es el que usan los atacantes para recibir una _reverse shell_ constantemente.
    
- **`0 0 * * *`:** Se ejecuta exactamente a las **00:00 (medianoche) todos los días**. Ideal para backups diarios.
    
- **`*/15 * * * *`:** El uso de `*/N` significa "cada N". En este caso, se ejecutaría **cada 15 minutos**.
    
- **`0 9-17 * * 1-5`:** Se ejecuta a las **horas en punto (`0`), de 9 a 5 de la tarde (`9-17`), de lunes a viernes (`1-5`)**. El truco perfecto para automatizar reportes solo en horario laboral.