El término **demonio** (*daemon* en inglés) en informática fue acuñado en **1963** por los informáticos del equipo del proyecto MAC en el MIT (Instituto de Tecnología de Massachusetts), liderado por Fernando Corbató.

Inspirados en la física y la mitología, eligieron el nombre basándose en el **Demonio de Maxwell** (un ser hipotético de un experimento mental del físico James Clerk Maxwell) que trabajaba incansablemente en segundo plano ordenando moléculas, de forma invisible y sin intervención humana.

---

### 📌 Características de un Demonio informático

* **Trabaja en segundo plano:** No interactúa directamente con el usuario ni tiene interfaz gráfica.
* **Se ejecuta continuamente:** Se inicia al arrancar el sistema operativo y queda a la espera de eventos o peticiones.
* **Nomenclatura en Linux/UNIX:** Los procesos demonio se identifican técnicamente porque el nombre de su ejecutable termina con la letra **`d`**.

---

### 🛠️ Ejemplos comunes en Linux

* **`sshd`** (*Secure Shell Daemon*): El proceso que escucha en el puerto 22 a la espera de conexiones SSH salientes o entrantes.
* **`httpd` / `mysqld**`: Demonios encargados de servir páginas web (Apache) o gestionar bases de datos (MySQL).
* **`systemd`**: El demonio principal de inicialización en Linux (PID 1) que se encarga de arrancar y gestionar al resto de los demonios del sistema.

*(En sistemas Microsoft Windows, a estos mismos procesos en segundo plano se les llama **Servicios**).*