
| **Parámetro** | **Función**                                | **Ejemplo de sintaxis**                  |
| ------------- | ------------------------------------------ | ---------------------------------------- |
| `-o`          | Especifica el nombre del archivo de salida | `cc -o programa programa.c`              |
| `-c`          | Compila sin enlazar (genera archivo .o)    | `cc -c archivo.c`                        |
| `-g`          | Incluye información de depuración          | `cc -g -o programa programa.c`           |
| `-O`          | Nivel de optimización (0,1,2,3,s)          | `cc -O2 -o optimizado programa.c`        |
| `-Wall`       | Habilita todos los warnings                | `cc -Wall -o programa programa.c`        |
| `-I`          | Añade directorio para archivos de cabecera | `cc -I./includes -o programa programa.c` |
| `-L`          | Añade directorio para bibliotecas          | `cc -L./lib -o programa programa.c`      |
| `-l`          | Enlaza con biblioteca específica           | `cc -lm -o programa programa.c`          |
| `-D`          | Define macro para preprocesador            | `cc -DDEBUG -o programa programa.c`      |
| `-E`          | Solo preprocesamiento (no compila)         | `cc -E programa.c > salida.i`            |
| `-S`          | Genera código assembler                    | `cc -S programa.c`                       |
| `-std`        | Especifica estándar C (c99, c11, etc.)     | `cc -std=c11 -o programa programa.c`     |
