
## Monitorizar procesos a nivel de sistema a tiempo real

Muestra todos los comandos que se estan ejecutando en tiempo real a nivel del sistema

```bash
#!/bin/bash
old_process=$(ps -eo command)

while true: do
	new_process=$(ps -eo command)
	diff <(echo "$old_process") <(echo "$new_process") | grep -v -E "nombre_de_este_script|command"
done
```

### Explicación
1. El comando `ps -eo command` nos da los procesos que es están ejecutando a nivel del sistema. Y los guardamos en la variable `old_process`
2. Creamos un bucle while infinito(siempre es true)
3. Creamos una nueva variable `new_process` y le asignamos el mismo comando, y como hay una diferencia de ejecucion entre `old_process` y `new_process`, con diff vemos esa diferencia de los procesos antiguos y el nuevo que acaba de ejecutarse.
4. Con diff vemos la diferencia entre el viejo y el nuevo pero debemos de excluir el propio nombre del script y la ejecución del comando que utilizamos para ver los propios procesos, para ello usamos `grep -v -E "palabras a excluir"`


## Generador secuencial de números

Mediante un bucle generamos todos los números de manera secuencial. Pero si lo hacemos de manera normal, si queremos que explícitamente empiece por 0, lo omitirá, para ello haremos lo siguiente para que empiece explícitamente por 0001 y acabe en el 9999.

```bash
#!/bin/bash
for i in {0001..9999}; do
	echo "$i"
done
```
