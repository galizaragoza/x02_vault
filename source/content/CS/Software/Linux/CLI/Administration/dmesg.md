
| **Parámetro**                 | **Función**                                                               | **Ejemplo de sintaxis** |
| ------------------------- | --------------------------------------------------------------------- | ------------------- |
| `-T` o `--ctime`          | Muestra timestamp legible por humanos                                 | `dmesg -T`          |
| `-H` o `--human`          | Salida legible con colores y paginación                               | `dmesg -H`          |
| `-w` o `--follow`         | Muestra nuevos mensajes en tiempo real                                | `dmesg -w`          |
| `-l` o `--level=`         | Filtra por nivel de log (emerg,alert,crit,err,warn,notice,info,debug) | `dmesg -l err,warn` |
| `-k` o `--kernel`         | Muestra solo mensajes del kernel                                      | `dmesg -k`          |
| `-u` o `--userspace`      | Muestra solo mensajes de userspace                                    | `dmesg -u`          |
| `-f` o `--facility=`      | Filtra por facilidad (kern,user,mail,daemon,auth,syslog,lpr,news)     | `dmesg -f kern`     |
| `-n` o `--console-level=` | Establece nivel de log de la consola                                  | `dmesg -n 4`        |
| `-s` o `--buffer-size=`   | Usa buffer de tamaño específico                                       | `dmesg -s 8192`     |
| `-c` o `--clear`          | Limpia el buffer del ring del kernel                                  | `dmesg -c`          |
| `-r` o `--raw`            | Muestra mensaje en formato crudo                                      | `dmesg -r`          |
| `-D` o `--console-off`    | Deshabilita mensajes en consola                                       | `dmesg -D`          |
| `-E` o `--console-on`     | Habilita mensajes en consola                                          | `dmesg -E`          |
| `-C` o `--clear`          | Limpia buffer y no muestra nada                                       | `sudo dmesg -C`     |
