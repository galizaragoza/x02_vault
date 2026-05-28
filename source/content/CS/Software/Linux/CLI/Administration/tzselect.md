Herramienta interactiva que pregunta al usuario sobre su ubicación actual y genera la zona horaria resultante en salida estándar, adecuada para la variable TZ. Toda interacción es vía entrada/errores estándar.

| **Parámetro**   | **Función**                                                                                                          | **Ejemplo**                            |
| ----------- | ---------------------------------------------------------------------------------------------------------------- | ---------------------------------- |
| `-c coord`  | Selecciona zona horaria basada en coordenadas geográficas en lugar de continente/país/ciudad (formato ISO 6709). | `tzselect -c +40.689-074.045`      |
| `-n limit`  | Muestra las 'limit' ubicaciones más cercanas cuando se usa -c (predeterminado 10).                               | `tzselect -c +40.689-074.045 -n 5` |
| `--help`    | Muestra información de ayuda y sale.                                                                             | `tzselect --help`                  |
| `--version` | Muestra información de versión y sale.                                                                           | `tzselect --version`               |