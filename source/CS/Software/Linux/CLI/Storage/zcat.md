Herramienta para descomprimir y mostrar contenido de archivos gzip en stdout sin modificarlos. Equivalente a gzip -dc. Procesa múltiples archivos o stdin.

| Parámetro                 | Función                                                    | Ejemplo                         |
| ------------------------- | ---------------------------------------------------------- | ------------------------------- |
| `-f` o `--force`          | Fuerza lectura incluso desde terminal o enlaces múltiples. | `zcat -f archivo.gz`            |
| `-l` o `--list`           | Lista detalles de archivos comprimidos (tamaño, ratio).    | `zcat -l archivo.gz`            |
| `-q` o `--quiet`          | Suprime advertencias.                                      | `zcat -q archivo.gz`            |
| `-r` o `--recursive`      | Procesa directorios recursivamente.                        | `zcat -r dir/`                  |
| `-S SUF` o `--suffix=SUF` | Usa sufijo personalizado en lugar de .gz.                  | `zcat -S .tgz archivo.tgz`      |
| `--synchronous`           | Salida síncrona (más segura pero lenta).                   | `zcat --synchronous archivo.gz` |
| `-t` o `--test`           | Prueba integridad de archivo.                              | `zcat -t archivo.gz`            |
| `-v` o `--verbose`        | Muestra información detallada.                             | `zcat -v archivo.gz`            |
| `--help`                  | Muestra ayuda.                                             | `zcat --help`                   |
| `--version`               | Muestra versión.                                           | `zcat --version`                |