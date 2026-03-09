

| **Parámetro**  | **Función**                                   | **Sintaxis de Ejemplo**  |
| ---------- | ----------------------------------------- | -------------------- |
| `-u`       | Estadísticas de CPU                       | `pidstat -u`         |
| `-r`       | Estadísticas de uso de memoria            | `pidstat -r`         |
| `-d`       | Estadísticas de I/O de disco              | `pidstat -d`         |
| `-p PID`   | Monitorear proceso específico             | `pidstat -p 1234`    |
| `-C comm`  | Monitorear por nombre de comando          | `pidstat -C firefox` |
| `-t`       | Mostrar hilos asociados                   | `pidstat -t -p 1234` |
| `-h`       | Todas las estadísticas en formato legible | `pidstat -h 2 5`     |
| `interval` | Intervalo entre reportes (segundos)       | `pidstat 2`          |
| `count`    | Número de reportes a generar              | `pidstat 2 10`       |
| `-l`       | Mostrar línea de comando completa         | `pidstat -l`         |
| `-s`       | Estadísticas de pila y memoria            | `pidstat -s`         |