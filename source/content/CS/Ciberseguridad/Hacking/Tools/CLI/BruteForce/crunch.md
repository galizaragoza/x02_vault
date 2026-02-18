Crunch es una herramienta cuyo propósito principal es de generar diccionarios custom con los que hacer ataques en base a información obtenida previamente.

| Opción                        | Función principal                                                               | Ejemplo práctico                                        |
| ----------------------------- | ------------------------------------------------------------------------------- | ------------------------------------------------------- |
| `<min> <max>`                 | Longitud mínima y máxima de las palabras                                        | `crunch 8 8` → solo 8 caracteres                        |
| `[charset]`                   | Conjunto de caracteres a usar (si no se indica, usa abcdefghijklmnopqrstuvwxyz) | `crunch 6 6 abc123`                                     |
| `-f /path/charset.lst nombre` | Usa charset predefinido (hex-lower, mixalpha-numeric, etc.)                     | `-f /usr/share/crunch/charset.lst mixalpha-numeric-all` |
| `-o archivo`                  | Guarda la wordlist en archivo (obligatorio si no quieres salida por pipe)       | `-o wordlist.txt`                                       |
| `-t patrón`                   | Define patrón exacto: @=minúsc, ,=mayúsc, %=dígito, ^=símbolo                   | `-t @@@%%%` → aaa000 hasta zzz999                       |
| `-l patrón`                   | Indica qué caracteres del -t son literales (para usar @ , % ^ como texto)       | `-t -l @@@d@d@d 123-@@@-@@@-@@@@`                       |
| `-p palabra1 palabra2 …`      | Permutaciones de palabras/caracteres dados (ignora min/max)                     | `-p admin root user`                                    |
| `-q archivo`                  | Permutaciones de todas las líneas de un archivo (igual que -p pero desde txt)   | `-q usuarios.txt`                                       |
| `-s string`                   | Punto de inicio (permite reanudar)                                              | `-s aaaa0000`                                           |
| `-e string`                   | Punto donde detener la generación                                               | `-e aaaz9999`                                           |
| `-d número`                   | Limita repeticiones consecutivas del mismo carácter (reduce tamaño)             | `-d 2@` → máximo 2 letras iguales seguidas              |
| `-b tamaño[kmg]`              | Divide la salida en varios archivos del tamaño indicado (ideal para FAT32)      | `-b 500mb` → archivos de 500 MB                         |
| `-z gzip`                     | 7z                                                                              | bzip2`                                                  |
