rsync es una herramienta de sincronización y copia de archivos que minimiza la transferencia de datos usando algoritmos de delta encoding. Es eficiente para copias incrementales y sincronizaciones.
**

| **Parámetro**      | **Función**                                              | **Ejemplo de sintaxis**                                         |
| -------------- | ---------------------------------------------------- | ----------------------------------------------------------- |
| `-a`           | Modo archivo (preserva permisos, tiempos, etc.)      | `rsync -a origen/ destino/`                                 |
| `-v`           | Salida verbosa                                       | `rsync -av origen/ destino/`                                |
| `-z`           | Compresión durante la transferencia                  | `rsync -az origen/ destino/`                                |
| `-r`           | Copia recursiva (directorios)                        | `rsync -r origen/ destino/`                                 |
| `--delete`     | Elimina archivos en destino que no están en origen   | `rsync -a --delete origen/ destino/`                        |
| `--exclude`    | Excluye patrones específicos                         | `rsync -a --exclude='*.tmp' origen/ destino/`               |
| `--include`    | Incluye patrones específicos                         | `rsync -a --include='*.txt' --exclude='*' origen/ destino/` |
| `-n`           | Simulación (dry-run)                                 | `rsync -an origen/ destino/`                                |
| `-P`           | Muestra progreso y continúa transferencias parciales | `rsync -aP origen/ destino/`                                |
| `-h`           | Salida en formato legible para humanos               | `rsync -ah origen/ destino/`                                |
| `--stats`      | Muestra estadísticas de la transferencia             | `rsync -a --stats origen/ destino/`                         |
| `-e`           | Especifica shell remoto alternativo                  | `rsync -e "ssh -p 2222" origen/ usuario@host:destino/`      |
| `--bwlimit`    | Limita ancho de banda (KB/s)                         | `rsync --bwlimit=1000 origen/ destino/`                     |
| `-L`           | Copia referencias simbólicas como archivos           | `rsync -aL origen/ destino/`                                |
| `--backup`     | Crea backups de archivos existentes                  | `rsync -a --backup origen/ destino/`                        |
| `--backup-dir` | Directorio específico para backups                   | `rsync -a --backup --backup-dir=/backups origen/ destino/`  |