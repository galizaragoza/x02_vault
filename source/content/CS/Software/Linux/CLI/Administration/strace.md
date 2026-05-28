strace es una herramienta de diagnóstico, depuración e instrucción para Linux que rastrea las llamadas al sistema y señales de un proceso. Intercepta y registra las llamadas al sistema, sus argumentos y valores de retorno, así como señales recibidas. Útil para depurar programas sin código fuente, analizar interacciones con el kernel y resolver problemas.

| **Flag**             | **Descripción**                                                                                                                  | **Ejemplo**                           |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| `-a column`      | Alinea los valores de retorno en la columna especificada (por defecto 40).                                                   | `strace -a 30 ls`                 |
| `-c`             | Cuenta tiempo, llamadas y errores por llamada al sistema; muestra resumen al final.                                          | `strace -c ls`                    |
| `-d`             | Muestra salida de depuración de strace en stderr.                                                                            | `strace -d ls`                    |
| `-e expr`        | Modifica eventos a rastrear (ej: trace para syscalls específicas, abbrev, verbose, raw, signal, read, write, fault, inject). | `strace -e open ls`               |
| `-f`             | Rastrea procesos hijos creados por fork/vfork/clone.                                                                         | `strace -f ./program`             |
| `-ff`            | Rastrea hijos y escribe salida en archivos separados (filename.pid).                                                         | `strace -ff -o out ./program`     |
| `-i`             | Imprime puntero de instrucción en cada syscall.                                                                              | `strace -i ls`                    |
| `-k`             | Imprime stack trace después de cada syscall.                                                                                 | `strace -k ls`                    |
| `-o file`        | Escribe salida en archivo en lugar de stderr.                                                                                | `strace -o trace.txt ls`          |
| `-p pid`         | Adjunta a proceso con PID y rastrea.                                                                                         | `strace -p 1234`                  |
| `-q`             | Suprime mensajes de attach/detach (niveles: -q, -qq, -qqq).                                                                  | `strace -q ls`                    |
| `-r`             | Imprime timestamp relativo antes de cada línea.                                                                              | `strace -r ls`                    |
| `-s size`        | Tamaño máximo de strings impresos (por defecto 32).                                                                          | `strace -s 64 ls`                 |
| `-t`             | Imprime timestamp absoluto al inicio de cada línea.                                                                          | `strace -t ls`                    |
| `-tt`            | Timestamp con microsegundos.                                                                                                 | `strace -tt ls`                   |
| `-ttt`           | Timestamp con microsegundos y segundos desde epoch.                                                                          | `strace -ttt ls`                  |
| `-T`             | Muestra tiempo gastado en cada syscall.                                                                                      | `strace -T ls`                    |
| `-u username`    | Ejecuta comando como usuario especificado (útil como root).                                                                  | `strace -u user ./program`        |
| `-v`             | Modo verbose: imprime versiones no abreviadas de argumentos.                                                                 | `strace -v ls`                    |
| `-x`             | Imprime strings no-ASCII en hexadecimal.                                                                                     | `strace -x ls`                    |
| `-xx`            | Imprime todos los strings en hexadecimal.                                                                                    | `strace -xx ls`                   |
| `-y`             | Imprime paths asociados a descriptores de archivo.                                                                           | `strace -y ls`                    |
| `-yy`            | Imprime info específica de protocolos para sockets.                                                                          | `strace -yy ls`                   |
| `-z`             | Imprime solo syscalls fallidas.                                                                                              | `strace -z ls`                    |
| `-Z`             | Imprime solo syscalls exitosas.                                                                                              | `strace -Z ls`                    |
| `-A file`        | Abre y append salida a archivo (crea si no existe).                                                                          | `strace -A log.txt ls`            |
| `-b syscall`     | Detacha al alcanzar syscall especificada (ej: execve).                                                                       | `strace -b execve ./program`      |
| `-D`             | Ejecuta tracer como nieto del tracee para menor impacto.                                                                     | `strace -D ls`                    |
| `-E var=val`     | Establece variable de entorno para el comando.                                                                               | `strace -E PATH=/bin ls`          |
| `--follow-forks` | Equivalente a -f (forma larga).                                                                                              | `strace --follow-forks ./program` |
| `--output=file`  | Equivalente a -o (forma larga).                                                                                              | `strace --output=trace.txt ls`    |