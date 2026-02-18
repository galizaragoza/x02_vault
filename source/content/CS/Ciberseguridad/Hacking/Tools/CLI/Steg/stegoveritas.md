Detectar y extraer datos ocultos en imágenes y archivos mediante análisis de esteganografía (metadata, LSB, Steghide, carving).

```
stegoveritas [opciones] archivo
```

| **Parámetro**       | **Función**                                                 | **Ejemplo de uso**                                       |
| --------------- | ------------------------------------------------------- | ---------------------------------------------------- |
| -exif           | Extrae metadatos EXIF                                   | stegoveritas -exif image.jpg                         |
| -xmp            | Extrae metadatos XMP                                    | stegoveritas -xmp photo.png                          |
| -steghide       | Busca y extrae datos con Steghide (brute con wordlist)  | stegoveritas -steghide -wordlist pass.txt secret.bmp |
| -carve          | Carvea archivos incrustados (usa foremost)              | stegoveritas -carve embedded.gif                     |
| -trailing       | Detecta datos trailing en el archivo                    | stegoveritas -trailing taildata.wav                  |
| -bruteLSB       | Brute-force en bits menos significativos (LSB)          | stegoveritas -bruteLSB noisy.png                     |
| -extractLSB     | Extrae datos de canales LSB (R/G/B/Alpha)               | stegoveritas -extractLSB -red 1 image.tiff           |
| -imageTransform | Aplica transformaciones (contraste, color) para revelar | stegoveritas -imageTransform altered.jpg             |
| -meta           | Análisis general de metadatos                           | stegoveritas -meta doc.pdf                           |
| -out dir        | Directorio de salida para resultados                    | stegoveritas -out /tmp/results file.bin              |