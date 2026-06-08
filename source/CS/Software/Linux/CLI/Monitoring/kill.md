| **Parámetro/Señal** | **Función**                                  | **Sintaxis de Ejemplo** |
| ------------------- | -------------------------------------------- | ----------------------- |
| `-l` o `-L`         | Lista todas las señales disponibles          | `kill -l`               |
| `-s SIGNAL`         | Especifica la señal por nombre               | `kill -s TERM 1234`     |
| `-SIGNAL`           | Especifica la señal por número               | `kill -9 1234`          |
| `PID`               | PID del proceso destino                      | `kill 1234`             |
| `-n SIGNUM`         | Especifica la señal por número (alternativa) | `kill -n 15 1234`       |

## killall
| **Parámetro**                       | **Función**                                              | **Sintaxis de Ejemplo**       |
| ------------------------------- | ---------------------------------------------------- | ------------------------- |
| `-e` o `--exact`                | Coincidencia exacta del nombre (largo)               | `killall -e firefox`      |
| `-I` o `--ignore-case`          | Ignorar mayúsculas/minúsculas                        | `killall -I FIREFOX`      |
| `-i` o `--interactive`          | Modo interactivo (pregunta confirmación)             | `killall -i process_name` |
| `-r` o `--regexp`               | Usar expresión regular para nombres                  | `killall -r '^python.*'`  |
| `-s SIGNAL` o `--signal SIGNAL` | Especificar señal a enviar                           | `killall -s HUP nginx`    |
| `-u USER` o `--user USER`       | Solo procesos del usuario especificado               | `killall -u www-data`     |
| `-v` o `--verbose`              | Modo verbose (muestra detalles)                      | `killall -v python`       |
| `-w` o `--wait`                 | Esperar a que procesos terminen                      | `killall -w apache2`      |
| `-y` o `--younger-than`         | Solo procesos más jóvenes que el tiempo especificado | `killall -y 1h process`   |
| `-o` o `--older-than`           | Solo procesos más viejos que el tiempo especificado  | `killall -o 24h process`  |


# Tabla de Señales Comunes

| **Señal**              | **Número** | **Función Principal**                   | **Ejemplo**           |
| ------------------ | ------ | ----------------------------------- | ----------------- |
| `TERM` o `SIGTERM` | 15     | Terminación amable (default)        | `kill -TERM 1234` |
| `KILL` o `SIGKILL` | 9      | Terminación forzosa (no capturable) | `kill -9 1234`    |
| `HUP` o `SIGHUP`   | 1      | Recarga/terminación (hang up)       | `kill -HUP 1234`  |
| `INT` o `SIGINT`   | 2      | Interrupción (Ctrl+C)               | `kill -INT 1234`  |
| `QUIT` o `SIGQUIT` | 3      | Salida con core dump (Ctrl+)        | `kill -QUIT 1234` |
| `STOP` o `SIGSTOP` | 19     | Pausa proceso (no capturable)       | `kill -STOP 1234` |
| `CONT` o `SIGCONT` | 18     | Reanuda proceso pausado             | `kill -CONT 1234` |
| `USR1`             | 10     | Señal definida por usuario 1        | `kill -USR1 1234` |
| `USR2`             | 12     | Señal definida por usuario 2        | `kill -USR2 1234` |
| `TSTP` o `SIGTSTP` | 20     | Pausa terminal (Ctrl+Z)             | `kill -TSTP 1234` |