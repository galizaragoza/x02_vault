
| **Parámetro**             | **Función**                                                                   | **Ejemplo**                                |
| --------------------- | ------------------------------------------------------------------------- | -------------------------------------- |
| `-c` o `--stdout`     | Escribe salida comprimida a stdout, mantiene original.                    | `gzip -c archivo.txt > archivo.txt.gz` |
| `-d` o `--decompress` | Descomprime archivos .gz.                                                 | `gzip -d archivo.txt.gz`               |
| `-f` o `--force`      | Fuerza compresión/descompresión incluso con enlaces múltiples o terminal. | `gzip -f archivo.txt`                  |
| `-k`                  | Mantiene el archivo original tras compresión.                             | `gzip -k archivo.txt`                  |
| `-l` o `--list`       | Lista detalles de archivos comprimidos (tamaño, ratio).                   | `gzip -l archivo.txt.gz`               |
| `-r` o `--recursive`  | Procesa directorios recursivamente.                                       | `gzip -r directorio/`                  |
| `-t` o `--test`       | Prueba integridad de archivo comprimido.                                  | `gzip -t archivo.txt.gz`               |
| `-v` o `--verbose`    | Muestra nombre y reducción porcentual.                                    | `gzip -v archivo.txt`                  |
| `-1` a `-9`           | Nivel de compresión (1 rápido/menor, 9 lento/mejor).                      | `gzip -9 archivo.txt`                  |
| `-S .suf`             | Usa sufijo personalizado en lugar de .gz.                                 | `gzip -S .tgz archivo.tar`             |