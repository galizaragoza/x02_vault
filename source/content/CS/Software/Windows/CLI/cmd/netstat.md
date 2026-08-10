

| **Parámetro**       | **Función**                                 | **Sintaxis de Ejemplo** |
| --------------- | --------------------------------------- | ------------------- |
| `-t`            | Mostrar conexiones TCP                  | `netstat -t`        |
| `-u`            | Mostrar conexiones UDP                  | `netstat -u`        |
| `-l`            | Mostrar sockets en escucha (listening)  | `netstat -l`        |
| `-n`            | Mostrar direcciones numéricas (sin DNS) | `netstat -n`        |
| `-p`            | Mostrar PID/programa de cada socket     | `netstat -p`        |
| `-a`            | Mostrar todos los sockets               | `netstat -a`        |
| `-r`            | Mostrar tabla de enrutamiento           | `netstat -r`        |
| `-i`            | Mostrar estadísticas de interfaces      | `netstat -i`        |
| `-s`            | Mostrar estadísticas por protocolo      | `netstat -s`        |
| `-c`            | Actualizar continuamente                | `netstat -c 2`      |
| `-e`            | Mostrar información extendida           | `netstat -e`        |
| `--tcp`/`--udp` | Filtrar por protocolo específico        | `netstat --tcp`     |
| `--program`     | Mostrar programa asociado               | `netstat --program` |
