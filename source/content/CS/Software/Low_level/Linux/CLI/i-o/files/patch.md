| **Parámetro**             | **Descripción**                                                 | **Ejemplo de sintaxis**                                          |
| --------------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| `-p num`              | Elimina `num` componentes de ruta de los nombres de archivo | `patch -p1 < cambios.patch`  <br>`patch -p0 < cambios.patch` |
| `-i archivo`          | Especifica el archivo de parche a aplicar                   | `patch -i cambios.diff`                                      |
| `-o archivo`          | Especifica el archivo de salida                             | `patch -o resultado.txt original.txt < cambios.patch`        |
| `-R` o `--reverse`    | Aplica el parche en reversa (deshacer cambios)              | `patch -R < cambios.patch`                                   |
| `-E`                  | Elimina archivos vacíos después de aplicar parche           | `patch -E < cambios.patch`                                   |
| `-b` o `--backup`     | Crea copias de seguridad de los archivos originales         | `patch -b < cambios.patch`                                   |
| `--dry-run`           | Simula la aplicación sin modificar archivos                 | `patch --dry-run < cambios.patch`                            |
| `-s` o `--silent`     | Modo silencioso, solo muestra errores                       | `patch -s < cambios.patch`                                   |
| `-v` o `--verbose`    | Modo verboso, muestra más información                       | `patch -v < cambios.patch`                                   |
| `-d dir`              | Cambia al directorio `dir` antes de aplicar                 | `patch -d /ruta/al/proyecto < cambios.patch`                 |
| `-F num`              | Establece el máximo de líneas de contexto a ignorar         | `patch -F 3 < cambios.patch`                                 |
| `--ignore-whitespace` | Ignora cambios en espacios en blanco                        | `patch --ignore-whitespace < cambios.patch`                  |
| `-t` o `--batch`      | Ignora preguntas, asume respuestas por defecto              | `patch -t < cambios.patch`                                   |