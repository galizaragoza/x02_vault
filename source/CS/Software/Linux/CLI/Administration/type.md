
| **Parámetro**      | **Función**                                                        | **Sintaxis de Ejemplo** |
| -------------- | -------------------------------------------------------------- | ------------------- |
| Sin parámetros | Muestra información básica sobre el comando                    | `type ls`           |
| `-t`           | Muestra solo el tipo (alias, keyword, function, builtin, file) | `type -t ls`        |
| `-p`           | Muestra la ruta del ejecutable (solo si es un archivo)         | `type -p python`    |
| `-a`           | Muestra todas las ubicaciones/definiciones posibles            | `type -a echo`      |
| `-P`           | Fuerza la búsqueda en PATH incluso para built-ins              | `type -P echo`      |
| `-f`           | Suprime la búsqueda de funciones (en algunos shells)           | `type -f command`   |