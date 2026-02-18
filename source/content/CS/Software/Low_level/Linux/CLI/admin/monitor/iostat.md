Monitorea carga I/O de dispositivos y uso de CPU.

| **Parámetro**          | **Función**                                  | **Ejemplo**            |
| ------------------ | ---------------------------------------- | ------------------ |
| `-c`               | Muestra reporte CPU.                     | `iostat -c`        |
| `-d`               | Muestra reporte dispositivos.            | `iostat -d`        |
| `-k`               | Estadísticas en KiB/s.                   | `iostat -k`        |
| `-m`               | Estadísticas en MiB/s.                   | `iostat -m`        |
| `-p device`        | Estadísticas de dispositivo/particiones. | `iostat -p sda`    |
| `-x`               | Estadísticas extendidas.                 | `iostat -x`        |
| `-s`               | Versión corta.                           | `iostat -s`        |
| `-t`               | Agrega timestamp.                        | `iostat -t`        |
| `-y`               | Omite primer reporte.                    | `iostat -y`        |
| `--human`          | Formato legible.                         | `iostat --human`   |
| `--compact`        | Métricas en una línea.                   | `iostat --compact` |
| `interval [count]` | Intervalo y conteo de reportes.          | `iostat -d 2 5`    |