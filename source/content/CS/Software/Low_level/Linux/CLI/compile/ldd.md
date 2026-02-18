`ldd` (List Dynamic Dependencies) es una utilidad que muestra las bibliotecas compartidas (shared libraries) requeridas por un ejecutable o por otra biblioteca compartida. Es útil para diagnosticar problemas de ejecución relacionados con bibliotecas faltantes o rutas incorrectas.

| **Parámetro**                  | **Función**                                           | **Ejemplo de sintaxis**        |
| -------------------------- | ------------------------------------------------- | -------------------------- |
| `-v` o `--verbose`         | Salida detallada (muestra información de versión) | `ldd -v /bin/ls`           |
| `-u` o `--unused`          | Muestra dependencias no usadas                    | `ldd -u /usr/bin/programa` |
| `-d` o `--data-relocs`     | Realiza relocalizaciones de datos                 | `ldd -d programa`          |
| `-r` o `--function-relocs` | Realiza relocalizaciones de datos y funciones     | `ldd -r programa`          |
| `--help`                   | Muestra ayuda de uso                              | `ldd --help`               |
| `--version`                | Muestra versión                                   | `ldd --version`            |
| `-V`                       | Muestra información del linker                    | `ldd -V`                   |
| Sin parámetros             | Muestra dependencias básicas                      | `ldd /bin/bash`            |