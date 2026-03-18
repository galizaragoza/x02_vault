`badblocks` es una utilidad de bajo nivel diseñada para buscar sectores defectuosos en un dispositivo (generalmente una partición de disco). Es una herramienta crítica para el diagnóstico de fallos físicos inminentes.

| **Parámetro** | **Función**                                                                                | **Ejemplo de Sintaxis**              |
| ------------- | ------------------------------------------------------------------------------------------ | ------------------------------------ |
| `-v`          | Modo "verbose": muestra el progreso y los detalles de los errores encontrados.             | `badblocks -v /dev/sdb1`             |
| `-n`          | Modo lectura-escritura no destructivo (prueba de escritura que restaura el dato original). | `badblocks -n /dev/sdb1`             |
| `-w`          | Modo escritura destructivo (escribe patrones para verificar integridad). **Borra datos**.  | `badblocks -w /dev/sdb1`             |
| `-s`          | Muestra el progreso de la operación en porcentaje.                                         | `badblocks -s /dev/sdb1`             |
| `-o [file]`   | Escribe la lista de bloques dañados en un archivo de texto.                                | `badblocks -o damaged.txt /dev/sdb1` |
| `-b [size]`   | Especifica el tamaño de bloque en bytes (por defecto 1024).                                | `badblocks -b 4096 /dev/sdb1`        |
| `-p [num]`    | Repite el escaneo el número de veces indicado hasta que no aparezcan bloques nuevos.       | `badblocks -p 2 /dev/sdb1`           |