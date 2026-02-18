Herramienta para crear y manipular archivos de archivo (archives), almacenando múltiples archivos en uno solo. Soporta compresión con gzip, bzip2, etc. Es una sola utilidad.

| **Parámetro**                | **Función**                                            | **Ejemplo**                                    |
| ---------------------------- | ------------------------------------------------------ | ---------------------------------------------- |
| `-c` o `--create`            | Crea un nuevo archivo.                                 | `tar -c -f archive.tar /ruta/a/dir`            |
| `-x` o `--extract` o `--get` | Extrae archivos del archivo.                           | `tar -x -f archive.tar`                        |
| `-f` o `--file=ARCHIVE`      | Especifica el archivo o dispositivo.                   | `tar -c -f archive.tar /ruta/a/archivos`       |
| `-v` o `--verbose`           | Muestra archivos procesados.                           | `tar -c -v -f archive.tar /ruta/a/archivos`    |
| `-z`                         | Filtra a través de gzip para compresión/descompresión. | `tar -c -z -f archive.tar.gz /ruta/a/archivos` |
| `-t` o `--list`              | Lista contenidos del archivo.                          | `tar -t -f archive.tar`                        |
| `-r` o `--append`            | Añade archivos al final de un archivo existente.       | `tar -r -f archive.tar nuevoarchivo.txt`       |
| `--exclude=PATTERN`          | Excluye archivos que coincidan con el patrón.          | `tar -c -f archive.tar /dir --exclude='*.tmp'` |
