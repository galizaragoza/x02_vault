Reports virtual memory statistics: processes, memory, paging, block IO, traps, disks, CPU activity.

| **Parámetro** | **Función**                                   | **Ejemplo**           |
| ------------- | --------------------------------------------- | --------------------- |
| `-a`          | Muestra memoria activa/inactiva.              | `vmstat -a`           |
| `-f`          | Muestra forks desde boot.                     | `vmstat -f`           |
| `-m`          | Muestra slabinfo.                             | `vmstat -m`           |
| `-n`          | Muestra header solo una vez.                  | `vmstat -n`           |
| `-s`          | Muestra contadores y estadísticas de memoria. | `vmstat -s`           |
| `-d`          | Muestra estadísticas de disco.                | `vmstat -d`           |
| `-D`          | Muestra resumen de actividad de disco.        | `vmstat -D`           |
| `-p device`   | Muestra estadísticas de partición.            | `vmstat -p /dev/sda1` |
| `-S unit`     | Cambia unidades de salida.                    | `vmstat -S m`         |
| `-t`          | Agrega timestamp.                             | `vmstat -t`           |
| `-w`          | Modo ancho.                                   | `vmstat -w`           |
| `-y`          | Omite primer reporte.                         | `vmstat -y`           |
| `-V`          | Muestra versión.                              | `vmstat -V`           |
| `-h`          | Muestra ayuda.                                | `vmstat -h`           |