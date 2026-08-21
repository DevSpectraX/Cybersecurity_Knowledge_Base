---
tags: [windows, registro, persistencia]
---

El Registro es una base de datos jerárquica centralizada donde Windows guarda configuraciones del sistema, usuarios y software.

## Las 5 Colmenas Principales (Hives)
1. `HKEY_CLASSES_ROOT (HKCR)`: Asociaciones de archivos y objetos OLE.
2. `HKEY_CURRENT_USER (HKCU)`: Configuración del usuario que ha iniciado sesión actualmente.
3. `HKEY_LOCAL_MACHINE (HKLM)`: Configuración global de todo el equipo (Hardware, Red, Seguridad). **Requiere privilegios de Administrador para modificar.**
4. `HKEY_USERS (HKU)`: Perfiles de todos los usuarios cargados en el equipo.
5. `HKEY_CURRENT_CONFIG (HKCC)`: Información del perfil de hardware actual.

## 📌 Claves típicas para Persistencia (Run Keys)
Tanto los administradores para arrancar programas legítimos, como los atacantes para mantener el acceso al reiniciar el equipo, usan estas rutas del registro:

* **Para el usuario actual (No requiere Admin):**
  `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
* **Para todo el equipo (Requiere Admin):**
  `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`

*Comando rápido para consultar vía CMD:*
```
reg query "HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run"
```