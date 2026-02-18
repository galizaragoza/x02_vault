| **Parámetro**              | **Función**                                     | **Ejemplo de sintaxis**                  |
| ---------------------- | ------------------------------------------- | ------------------------------------ |
| `-v` o `--verbose`     | Muestra el progreso                         | `shred -v archivo.conf`              |
| `-n` o `--iterations=` | Número de sobreescrituras (por defecto: 3)  | `shred -n 7 archivo.txt`             |
| `-z` o `--zero`        | Agrega sobreescritura final con ceros       | `shred -z archivo.txt`               |
| `-u` o `--remove`      | Elimina el archivo después de sobreescribir | `shred -u archivo.txt`               |
| `-f` o `--force`       | Fuerza permisos si es necesario             | `shred -f archivo.txt`               |
| `--random-source=`     | Especifica fuente de datos aleatorios       | `shred --random-source=/dev/urandom` |