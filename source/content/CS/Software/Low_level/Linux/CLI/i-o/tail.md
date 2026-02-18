| **Parámetro**       | **Función**                                                                    | **Ejemplo de Sintaxis**                 |
| --------------- | -------------------------------------------------------------------------- | ----------------------------------- |
| `-n <número>`   | Muestra las últimas `<número>` líneas (por defecto: 10)                    | `tail -n 20 archivo.log`            |
| `-f`            | Sigue el archivo en tiempo real (follow)                                   | `tail -f /var/log/syslog`           |
| `-F`            | Similar a `-f`, pero reabre el archivo si se renombra o elimina            | `tail -F aplicacion.log`            |
| `-c <bytes>`    | Muestra los últimos `<bytes>` del archivo                                  | `tail -c 500 archivo.bin`           |
| `-q`            | Modo silencioso (no muestra encabezados de archivo)                        | `tail -q archivo1.txt archivo2.txt` |
| `-v`            | Modo verboso (siempre muestra encabezados de archivo)                      | `tail -v *.log`                     |
| `--pid=<PID>`   | Termina cuando el proceso con el PID especificado finaliza                 | `tail -f log.txt --pid=1234`        |
| `-s <segundos>` | Intervalo de espera entre actualizaciones en modo `-f` (por defecto: 1.0s) | `tail -f -s 2.5 log.txt`            |
| `--retry`       | Reintenta abrir un archivo si no está disponible                           | `tail --retry -f archivo.log`       |