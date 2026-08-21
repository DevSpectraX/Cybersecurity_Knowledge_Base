---
tags: [linux, procesos, servicios, administracion, sysadmin]
---
Entender cómo Linux gestiona la ejecución de programas (procesos) y las tareas que corren en segundo plano (servicios/daemons) es fundamental para detectar actividad maliciosa, analizar el rendimiento del sistema y buscar vectores de escalada de privilegios.



---

## 1. Gestión de Procesos

Un proceso es un programa en ejecución. Cada proceso tiene un identificador único conocido como **PID (Process ID)**.

### El Origen de Todo: PID 1
El primer proceso que arranca el Kernel de Linux se conoce como **Init** (en la mayoría de distribuciones modernas es `systemd`). Este proceso siempre tiene el **PID 1** y es el padre de todos los demás procesos del sistema.

### Comandos Esenciales de Enumeración

* **Listar procesos del usuario actual:**
  ```bash
  ps
  ```


#### Listar TODOS los procesos del sistema (Estilo BSD - El más usado):

```bash
ps
```

- **USER:** Usuario que ejecuta el proceso. (Ojo si un servicio web corre como `root`).

- **PID:** ID del proceso.

- **STAT:** Estado (R: Corriendo, S: Durmiendo, Z: Zombi).

- **COMMAND:** El comando exacto que levantó el proceso.

#### Ver procesos en tiempo real(interactivo)
```bash
top
```

#### Ver el árbol genealogico de los procesos
```bash
pstree -p
```

#### Matar procesos(Señales)
Para detener un proceso que se ha quedado colgado o que es malicioso, se usa el comando `kill` enviando una "señal":

- **Terminación ordenada (SIGTERM - Señal 15):** Le pide amablemente al proceso que cierre sus archivos y termine.
```bash
kill 15 [PID]
```

  
- **Terminación forzada (SIGKILL - Señal 9):** Obliga al Kernel a cerrar el proceso de golpe. No da tiempo a guardar nada.
```bash
kill -9 [PID]
```

## 2. Gestión de Servicios (systemd)

Los servicios (o _daemons_, que suelen terminar con una 'd', como `sshd`, `httpd`) son programas que corren en segundo plano sin interacción del usuario, esperando a realizar una tarea (ej: responder a una petición web). En sistemas modernos, se administran con la herramienta `systemctl`.

### Comandos de Control de Servicios (Requieren sudo)

- **Ver el estado de un servicio (ej: SSH)**
```bash
systemctl status ssh
```

- **Iniciar / Detener / Reiniciar**
```bash
sudo systemctl start apache2 sudo systemctl stop apache2 sudo systemctl restart apache2
```


- **Habilitar / Deshabilitar (Para que arranquen o no automáticamente al encender el PC):**

```bash
sudo systemctl enable ssh
sudo systemctl disable ssh
```


## Enfoque de Ciberseguridad (¿Qué buscar aquí?)

Cuando consigas acceso a una máquina Linux en un laboratorio, ejecuta `ps aux` y busca activamente estas dos cosas:

### A) Procesos de Root sospechosos

Si ves un script en Python o un binario desconocido corriendo en el entorno de `root`, investiga de dónde viene. Podría ser una tarea programada (_cron job_) ejecutando un archivo que tú, como usuario de bajos privilegios, tienes permiso para modificar. Si modificas ese archivo, el proceso de `root` ejecutará tu código.

### B) Servicios expuestos localmente

A veces, un servicio (como una base de datos MySQL o un panel de administración) está configurado para escuchar solo localmente (`localhost` / `127.0.0.1`). No lo verás desde fuera con `nmap`, pero al listar los procesos o servicios internos verás que está ahí. Podrás atacarlo usando técnicas de [[Pivoting y Port Forwarding]].

```bash
# Comando para ver qué puertos e IPs internas están escuchando procesos:
netstat -tuln

# O su alternativa moderna:
ss -tuln
```

_MOC de Referencia:_ [[MOC_Fundamentos_Sistemas]]