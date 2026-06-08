Crear volcados hexadecimales (hex dumps) de archivos o entrada estándar, y revertirlos a binario. Permite inspeccionar y manipular datos binarios de forma legible.

```
xxd [opciones] [archivo] [salida]

xxd -r [opciones] [hexdump] [salida]
```

| **Parámetro**        | **Función**                                          | **Ejemplo de sintaxis**                 |
| ---------------- | ------------------------------------------------ | ----------------------------------- |
| `-l N`           | Limita la longitud del dump a N bytes            | `xxd -l 100 archivo.bin`            |
| `-s [+/-]offset` | Inicia desde un offset (absoluto o relativo)     | `xxd -s +0x100 archivo.bin`         |
| `-c N`           | Bytes por línea (columnas)                       | `xxd -c 16 archivo.bin`             |
| `-g N`           | Agrupa bytes en grupos de N                      | `xxd -g 4 archivo.bin`              |
| `-b`             | Dump en bits binarios (en vez de hex)            | `xxd -b archivo.bin`                |
| `-p`             | Estilo plain (hex continuo sin offsets ni ASCII) | `xxd -p archivo.bin`                |
| `-u`             | Usa mayúsculas en hex                            | `xxd -u archivo.bin`                |
| `-r`             | Revertir hex dump a binario                      | `xxd -r hex.txt > binario.original` |
| `-i`             | Salida como array C (variables)                  | `xxd -i archivo.bin`                |
