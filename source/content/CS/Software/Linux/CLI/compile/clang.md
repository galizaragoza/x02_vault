`clang` es un compilador C, C++, Objective-C y Objective-C++ desarrollado como parte del proyecto LLVM. Es conocido por sus mensajes de error más claros, diagnósticos avanzados y tiempos de compilación rápidos. Clang está diseñado para ser compatible con GCC.

| **Parámetro**             | **Función**                                            | **Ejemplo de sintaxis**                          |
| --------------------- | -------------------------------------------------- | -------------------------------------------- |
| `-o`                  | Especifica el nombre del archivo de salida         | `clang -o programa programa.c`               |
| `-c`                  | Compila sin enlazar                                | `clang -c archivo.c`                         |
| `-g`                  | Información de depuración completa                 | `clang -g -o programa programa.c`            |
| `-O`                  | Niveles de optimización                            | `clang -O3 -o programa programa.c`           |
| `-Wall`               | Habilita todos los warnings                        | `clang -Wall -Wextra programa.c`             |
| `-Weverything`        | Habilita TODAS las advertencias (clang específico) | `clang -Weverything programa.c`              |
| `-fsanitize`          | Habilita sanitizers (address, undefined, etc.)     | `clang -fsanitize=address programa.c`        |
| `-fcolor-diagnostics` | Salida de errores con colores                      | `clang -fcolor-diagnostics programa.c`       |
| `-MJ`                 | Genera información para herramientas de build      | `clang -MJ compile_commands.json programa.c` |
| `-target`             | Especifica arquitectura objetivo                   | `clang -target x86_64-linux-gnu programa.c`  |
| `-emit-llvm`          | Genera código IR de LLVM                           | `clang -S -emit-llvm programa.c`             |
| `--analyze`           | Ejecuta análisis estático                          | `clang --analyze programa.c`                 |
| `-stdlib`             | Especifica biblioteca estándar (libc++, libstdc++) | `clang++ -stdlib=libc++ programa.cpp`        |
## clang-format

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-i`|Formatea archivos in-place|`clang-format -i archivo.c`|
|`--style`|Especifica estilo (LLVM, Google, etc.)|`clang-format --style=Google archivo.c`|
|`-n`|Muestra cambios sin aplicarlos|`clang-format -n archivo.c`|
|`--dump-config`|Muestra configuración actual|`clang-format --dump-config`|

## clang-tidy

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-checks`|Especifica checks a realizar|`clang-tidy -checks=* archivo.c`|
|`-fix`|Aplica correcciones automáticas|`clang-tidy -fix archivo.c`|
|`-p`|Usa compile_commands.json|`clang-tidy -p build/ archivo.c`|
|`--header-filter`|Filtra headers a analizar|`clang-tidy --header-filter=.* archivo.c`|