lsof lists open files by processes, including regular files, directories, devices, sockets, streams; supports selection by path, formatted/parsed output, repeat mode.

| **Parámetro**         | **Función**                                 | **Sintaxis de Ejemplo**      |
| ----------------- | --------------------------------------- | ------------------------ |
| `-p PID`          | Archivos abiertos por proceso           | `lsof -p 1234`           |
| `-c proceso`      | Archivos abiertos por nombre de comando | `lsof -c sshd`           |
| `-u usuario`      | Archivos abiertos por usuario           | `lsof -u root`           |
| `-i`              | Archivos de red (sockets)               | `lsof -i`                |
| `-i:puerto`       | Sockets en puerto específico            | `lsof -i:80`             |
| `-i tcp`/`-i udp` | Filtrar por protocolo                   | `lsof -i tcp`            |
| `-n`              | No resolver nombres de host             | `lsof -n -i`             |
| `-P`              | No resolver nombres de puerto           | `lsof -P -i`             |
| `+d dir`          | Archivos abiertos en directorio         | `lsof +d /var/log`       |
| `-t`              | Solo PIDs (modo terse)                  | `lsof -t -i:22`          |
| `-R`              | Mostrar PPID (PID padre)                | `lsof -R -p 1234`        |
| `-a`              | AND lógico entre opciones               | `lsof -a -u root -i tcp` |
| `-V`              | Mostrar proceso de búsqueda             | `lsof -V`                |
