Cualquier comando que haya definida en la `.bashrc` se va a ejecutar, después de entablar la conexión, pero hay un pequeño margen de tiempo, y nos aprovechamos de eso para crear una terminal bash dentro, y asi saltarnos cualquier función definida en la `.bashrc` que no hiciera por ejemplo autodeslogearnos automaticamente al conectarnos a un servidor

### Ejemplo
```bash
ssh usuario@ip bash
```
El servidor ejecuta `bash`, pero **no ves la consola normal** (el _prompt_ con el `usuario@máquina:~#`). No puedes usar el tabulador, ni las flechas del teclado, ni comandos interactivos como `nano` o `top`.
### Tambien podríamos espawnerar la psedudo- consola 

```bash
ssh -t webexample.com bash --norc
```