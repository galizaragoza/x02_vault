
```
exiftool [opciones] <archivo(s)>
```

|                       |                                                            |                                                          |
| --------------------- | ---------------------------------------------------------- | -------------------------------------------------------- |
| `-a`                  | Muestra todos los metadatos                                | exiftool -a image.jpg                                    |
| `-g`                  | Agrupa los metadatos por categoría (EXIF, IPTC, XMP, etc.) | exiftool -g image.jpg                                    |
| `-u`                  | Incluye etiquetas desconocidas o no estándar               | exiftool -u image.jpg                                    |
| `-r`                  | Procesa un directorio completo de forma recursiva          | exiftool -r /path/to/folder                              |
| `-ext`                | Filtra archivos por extensión                              | exiftool -ext jpg /path/to/folder                        |
| `-overwrite_original` | Sobreescribe el archivo original con nuevos metadatos      | exiftool -overwrite_original -Comment="Hacked" image.jpg |
| `-all=`               | Elimina todos los metadatos editablea                      | exiftool -all= image.jpg                                 |
| `-json`               | Genera salida en formato JSON                              | exiftool -json image.jpg                                 |
| `-csv`                | Genera salida en formato CSV                               | exiftool -csv /path/to/folder > metadata.csv             |
| `-tagsFromFile`       | Copia todos los metadatos de un archivo a otro             | exiftool -tagsFromFile source.jpg -all:all target.jpg    |
|                       |                                                            |                                                          |
