
| **Parámetro**      | **Función**                                        | **Ejemplo de sintaxis**      |
| -------------- | ---------------------------------------------- | ------------------------ |
| `-a`           | Mostrar todos los archivos (incluidos ocultos) | `tree -a`                |
| `-d`           | Mostrar solo directorios                       | `tree -d`                |
| `-l`           | Seguir enlaces simbólicos como directorios     | `tree -l`                |
| `-f`           | Mostrar rutas completas                        | `tree -f`                |
| `-i`           | No mostrar líneas de árbol (lista simple)      | `tree -i`                |
| `-L`           | Limitar profundidad del árbol                  | `tree -L 3`              |
| `-P`           | Patrón para incluir archivos (wildcard)        | `tree -P "*.py"`         |
| `-I`           | Patrón para excluir archivos (wildcard)        | `tree -I "*.log\|*.tmp"` |
| `--charset=`   | Especificar conjunto de caracteres             | `tree --charset=ASCII`   |
| `--filelimit=` | No mostrar directorios con más de N archivos   | `tree --filelimit=10`    |
| `-o`           | Redirigir salida a archivo                     | `tree -o arbol.txt`      |
| `-q`           | Mostrar caracteres no imprimibles como '?'     | `tree -q`                |
| `-s`           | Mostrar tamaño de archivos                     | `tree -s`                |
| `-h`           | Mostrar tamaños en formato legible             | `tree -h`                |
| `--du`         | Mostrar tamaño de directorios                  | `tree --du`              |
| `-D`           | Mostrar fecha de modificación                  | `tree -D`                |
| `-p`           | Mostrar permisos (como ls -l)                  | `tree -p`                |
| `-u`           | Mostrar propietario/usuario                    | `tree -u`                |
| `-g`           | Mostrar grupo propietario                      | `tree -g`                |
| `--inodes`     | Mostrar números de inodo                       | `tree --inodes`          |
| `--device`     | Mostrar números de dispositivo                 | `tree --device`          |
| `-t`           | Ordenar por fecha de modificación              | `tree -t`                |
| `-r`           | Ordenar en orden inverso                       | `tree -r`                |
| `--dirsfirst`  | Mostrar directorios primero                    | `tree --dirsfirst`       |