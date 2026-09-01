---
tags:
  - terminal
  - tmux
  - cheatsheet
  - devops
  - linux
---

**tmux** es un multiplexor de terminales que permite alternar y gestionar fácilmente varios programas o terminales desde una sola pantalla. Además, permite desacoplar procesos para que continúen ejecutándose en segundo plano aunque te desconectes del servidor o cierres la ventana.

> [!TIP] **El Prefijo (Prefix Key)**
> La mayoría de accesos directos dentro de `tmux` requieren presionar primero la combinación de teclas de prefijo:
> **`Ctrl + b`** *(presionar y soltar)*, seguido de la tecla correspondiente al comando.

---

## 🏛️ Jerarquía de tmux

- **Sesión (Session):** Un entorno de trabajo completo que contiene una o más ventanas. Persiste en segundo plano.
- **Ventana (Window):** Equivale a una pestaña dentro de una sesión.
- **Panel (Pane):** Divisiones físicas (verticales u horizontales) dentro de una misma ventana.

---

## 📋 Cheatsheet de Comandos y Atajos

### 1. Gestión de Sesiones (Sessions)

| Acción | Comando / Atajo |
| :--- | :--- |
| **Crear nueva sesión** | `tmux` |
| **Crear sesión con nombre** | `tmux new -s <nombre>` |
| **Listar sesiones activas** | `tmux ls` |
| **Reconectarse a la última sesión** | `tmux a` |
| **Reconectarse a una sesión específica** | `tmux a -t <nombre>` |
| **Desconectarse (Detach)** | `Prefix` + `d` |
| **Renombrar sesión actual** | `Prefix` + `$` |
| **Menú interactivo de sesiones** | `Prefix` + `s` |
| **Eliminar una sesión** | `tmux kill-session -t <nombre>` |
| **Cerrar todas las sesiones (Kill server)** | `tmux kill-server` |

---

### 2. Gestión de Ventanas (Windows)

| Acción | Atajo |
| :--- | :--- |
| **Crear ventana** | `Prefix` + `c` |
| **Renombrar ventana actual** | `Prefix` + `,` |
| **Ir a la siguiente ventana** | `Prefix` + `n` |
| **Ir a la ventana anterior** | `Prefix` + `p` |
| **Ir a la última ventana activa** | `Prefix` + `l` |
| **Seleccionar por número (0-9)** | `Prefix` + `0..9` |
| **Listar todas las ventanas** | `Prefix` + `w` |
| **Buscar ventana por texto** | `Prefix` + `f` |
| **Mover posición de la ventana** | `Prefix` + `.` |
| **Cerrar ventana actual** | `Prefix` + `&` |

---

### 3. Gestión de Paneles (Panes)

| Acción | Atajo |
| :--- | :--- |
| **Dividir horizontalmente (arriba/abajo)** | `Prefix` + `%` |
| **Dividir verticalmente (izquierda/derecha)** | `Prefix` + `"` |
| **Navegar entre paneles** | `Prefix` + `Flechas` *(o `Prefix` + `o`)* |
| **Mostrar números de panel** | `Prefix` + `q` |
| **Maximizar / Restaurar panel (Zoom)** | `Prefix` + `z` |
| **Cambiar tamaño del panel** | `Prefix` + `Ctrl + Flechas` |
| **Rotar distribuciones (Layouts)** | `Prefix` + `Espacio` |
| **Mover panel hacia adelante** | `Prefix` + `}` |
| **Mover panel hacia atrás** | `Prefix` + `{` |
| **Convertir panel en ventana independiente** | `Prefix` + `!` |
| **Cerrar panel actual** | `Prefix` + `x` |

---

### 4. Modo Copia y Navegación (Copy Mode)

> [!INFO] Entra en este modo para desplazarte por el historial de la terminal o copiar texto sin usar el ratón.

- **Entrar en modo copia:** `Prefix` + `[`
- **Salir del modo copia:** `q` o `Esc`

#### Navegación y Búsqueda:
- **Desplazar página:** `PgUp` / `PgDn`
- **Navegación estilo Vi:** `h` (izq), `j` (abajo), `k` (arriba), `l` (der)
- **Inicio / Fin de línea:** `0` / `$`
- **Buscar hacia adelante:** `/`
- **Buscar hacia atrás:** `?`

#### Selección y Copia:
- **Iniciar selección:** `v` *(en modo Vi)* o `Espacio`
- **Copiar selección:** `y` o `Enter`
- **Pegar el texto copiado:** `Prefix` + `]`
- **Listar buffers copiados:** `Prefix` + `=`

---

### 5. Modo Comando y Configuración

| Acción | Comando / Atajo |
| :--- | :--- |
| **Abrir línea de comandos interna** | `Prefix` + `:` |
| **Ver lista de todos los atajos** | `Prefix` + `?` |
| **Recargar archivo de configuración** | `Prefix` + `:` seguido de `source-file ~/.tmux.conf` |

---

### 6. Miscelánea e Información

- `Prefix` + `t` — Muestra un reloj digital gigante en el panel activo.
- `Prefix` + `D` — Desconecta a otros clientes conectados a la misma sesión.
- `tmux -V` — Muestra la versión instalada de tmux.
- `tmux info` — Muestra información detallada del servidor de tmux.