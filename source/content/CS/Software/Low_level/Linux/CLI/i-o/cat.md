| **Parámetro**      | **Función**                                                            | **Ejemplo de sintaxis**         |
| ------------------ | ---------------------------------------------------------------------- | ------------------------------- |
| `-A`               | Equivalente a `-vET` (muestra todos los caracteres especiales)         | `cat -A archivo.txt`            |
| `-b`               | Numera las líneas que no están vacías                                  | `cat -b archivo.txt`            |
| `-e`               | Equivalente a `-vE`                                                    | `cat -e archivo.txt`            |
| `-E`               | Muestra `$` al final de cada línea                                     | `cat -E archivo.txt`            |
| `-n`               | Numera todas las líneas (incluyendo vacías)                            | `cat -n archivo.txt`            |
| `-s`               | Suprime múltiples líneas vacías adyacentes                             | `cat -s archivo.txt`            |
| `-t`               | Equivalente a `-vT`                                                    | `cat -t archivo.txt`            |
| `-T`               | Muestra tabulaciones como `^I`                                         | `cat -T archivo.txt`            |
| `-u`               | Desactiva el almacenamiento en búfer (sin efecto en sistemas modernos) | `cat -u archivo.txt`            |
| `-v`               | Muestra caracteres no imprimibles (excepto tab y fin de línea)         | `cat -v archivo.bin`            |
| Sin parámetro      | Muestra el contenido del archivo                                       | `cat archivo.txt`               |
| Múltiples archivos | Concatena varios archivos                                              | `cat archivo1.txt archivo2.txt` |
| Con redirección    | Crea/sobrescribe archivo                                               | `cat > nuevo.txt`               |
| Con append         | Añade a archivo existente                                              | `cat >> existente.txt`          |
| Combinación        | Combina múltiples opciones                                             | `cat -n -s archivo.txt`         |

# batcat
| **Parámetro**                     | **Función**                                                     | **Ejemplo de sintaxis**                       |
| ----------------------------- | ----------------------------------------------------------- | ----------------------------------------- |
| `-l` o `--language=`          | Forzar lenguaje para resaltado de sintaxis                  | `bat --language=python archivo.py`        |
| `-n` o `--number`             | Mostrar números de línea                                    | `bat -n archivo.txt`                      |
| `-A` o `--show-all`           | Mostrar todos los caracteres (como `cat -A`)                | `bat -A archivo.txt`                      |
| `-p` o `--plain`              | Deshabilitar resaltado (modo simple)                        | `bat -p archivo.conf`                     |
| `--theme=`                    | Especificar tema de colores                                 | `bat --theme="TwoDark" archivo.py`        |
| `--style=`                    | Controlar elementos mostrados (numbers,changes,header,grid) | `bat --style="numbers,grid" archivo.txt`  |
| `--color=`                    | Cuándo usar colores (auto, never, always)                   | `bat --color=always archivo.log`          |
| `-r` o `--line-range=`        | Mostrar solo un rango de líneas (N:M)                       | `bat -r 10:20 archivo.txt`                |
| `--wrap=`                     | Controlar ajuste de línea (auto, never, character)          | `bat --wrap=never archivo.long`           |
| `--paging=`                   | Controlar paginación (auto, never, always)                  | `bat --paging=never archivo.txt`          |
| `--list-languages`            | Listar todos los lenguajes soportados                       | `bat --list-languages`                    |
| `--list-themes`               | Listar todos los temas disponibles                          | `bat --list-themes`                       |
| `--diff`                      | Resaltar cambios entre archivos                             | `bat --diff archivo1.txt archivo2.txt`    |
| `--file-name=`                | Especificar nombre de archivo para detección de lenguaje    | `bat --file-name="script.sh" archivo.txt` |
| `-f` o `--force-colorization` | Forzar colores incluso para tuberías                        | `echo "text" \| bat -f`                   |