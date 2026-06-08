**`xargs`** (arguments extended) es una utilidad de línea de comandos de Unix/Linux que construye y ejecuta comandos a partir de la entrada estándar (stdin). Su función principal es convertir una secuencia de argumentos de entrada en argumentos para otro comando, permitiendo procesar eficientemente listas de elementos que exceden los límites de longitud de argumentos del sistema.

```
comando | xargs [opciones] comando_a_ejecutar
```

| **Parámetro**                              | **Función**                                                                           | **Sintaxis de Ejemplo**                                      |
| -------------------------------------- | --------------------------------------------------------------------------------- | -------------------------------------------------------- |
| `-0`, `--null`                         | Trata los elementos de entrada como separados por nulos (útil con `find -print0`) | `find . -name "*.txt" -print0 \| xargs -0 rm`            |
| `-a <archivo>`, `--arg-file=<archivo>` | Lee argumentos desde un archivo en lugar de stdin                                 | `xargs -a lista.txt echo`                                |
| `-d <delim>`, `--delimiter=<delim>`    | Usa un delimitador personalizado en lugar de espacios/tabuladores                 | `echo "a,b,c" \| xargs -d, -n1 echo`                     |
| `-E <eof-str>`                         | Define una cadena de fin de archivo (EOF)                                         | `echo "1 2 3 STOP 4 5" \| xargs -E "STOP" echo`          |
| `-I <replace-str>`                     | Reemplaza la cadena especificada en el comando con cada argumento de entrada      | `ls *.txt \| xargs -I {} mv {} {}.bak`                   |
| `-L <num>`                             | Ejecuta el comando con hasta <num> líneas no vacías de entrada                    | `cat datos.txt \| xargs -L 2 echo`                       |
| `-n <num>`, `--max-args=<num>`         | Usa como máximo <num> argumentos por invocación del comando                       | `echo "1 2 3 4 5" \| xargs -n 2 echo`                    |
| `-p`, `--interactive`                  | Pide confirmación antes de ejecutar cada comando                                  | `ls *.log \| xargs -p rm`                                |
| `-P <num>`, `--max-procs=<num>`        | Ejecuta hasta <num> procesos en paralelo (0 = ilimitado)                          | `seq 10 \| xargs -P 4 -n1 sleep`                         |
| `-r`, `--no-run-if-empty`              | No ejecuta el comando si la entrada está vacía                                    | `find . -name "*.tmp" \| xargs -r rm`                    |
| `-s <num>`, `--max-chars=<num>`        | Límite máximo de caracteres por línea de comandos                                 | `find . -name "*.txt" \| xargs -s 10000 echo`            |
| `-t`, `--verbose`                      | Muestra el comando en stderr antes de ejecutarlo                                  | `echo "file1 file2" \| xargs -t rm`                      |
| `-x`, `--exit`                         | Sale si la línea de comandos excede el tamaño máximo                              | `find / -name "*" \| xargs -x ls -la`                    |
| `--show-limits`                        | Muestra los límites del sistema para la longitud de argumentos                    | `xargs --show-limits`                                    |
| `--process-slot-var=<var>`             | Establece variable de entorno para procesos paralelos                             | `seq 5 \| xargs -P 2 --process-slot-var=SLOT echo $SLOT` |
