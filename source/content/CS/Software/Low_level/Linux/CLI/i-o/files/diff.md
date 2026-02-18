
| **Parámetro**                 | **Descripción**                                         | **Ejemplo de sintaxis**                                                            |
| ------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------------ |
| `-u` o `-U num`           | Formato unificado con `num` líneas de contexto      | `diff -u archivo1.txt archivo2.txt`  <br>`diff -U 5 archivo1.txt archivo2.txt` |
| `-c`                      | Formato de contexto                                 | `diff -c archivo1.txt archivo2.txt`                                            |
| `-r`                      | Compara directorios recursivamente                  | `diff -r dir1/ dir2/`                                                          |
| `-N`                      | Trata archivos ausentes como vacíos                 | `diff -N archivo1.txt archivo2.txt`                                            |
| `-i`                      | Ignora diferencias en mayúsculas/minúsculas         | `diff -i archivo1.txt archivo2.txt`                                            |
| `-w`                      | Ignora diferencias en espacios en blanco            | `diff -w archivo1.txt archivo2.txt`                                            |
| `-q` o `--brief`          | Solo indica si los archivos difieren                | `diff -q archivo1.txt archivo2.txt`                                            |
| `-s`                      | Reporta cuando dos archivos son idénticos           | `diff -s archivo1.txt archivo2.txt`                                            |
| `-y`                      | Muestra diferencias lado a lado                     | `diff -y archivo1.txt archivo2.txt`                                            |
| `-a`                      | Trata todos los archivos como texto                 | `diff -a binario1 binario2`                                                    |
| `-p`                      | Muestra a qué función C pertenece cada cambio       | `diff -p archivo1.c archivo2.c`                                                |
| `--color`                 | Resalta diferencias con colores                     | `diff --color archivo1.txt archivo2.txt`                                       |
| `--suppress-common-lines` | En modo lado a lado, muestra solo líneas diferentes | `diff -y --suppress-common-lines archivo1.txt archivo2.txt`                    |