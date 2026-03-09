
| **Parámetro**            | **Función**                                                    | **Ejemplo de Sintaxis**               |
| -------------------- | ---------------------------------------------------------- | --------------------------------- |
| `updatedb`           | Comando para actualizar la base de datos de `locate`       | `sudo updatedb`                   |
| (sin parámetros)     | Busca patrones en la base de datos                         | `locate .bashrc`                  |
| `-i`                 | Búsqueda insensible a mayúsculas/minúsculas                | `locate -i documento.pdf`         |
| `-c`                 | Solo cuenta los resultados, no los muestra                 | `locate -c *.jpg`                 |
| `-l`, `--limit`      | Limita el número de resultados mostrados                   | `locate -l 10 *.log`              |
| `-S`, `--statistics` | Muestra estadísticas de la base de datos                   | `locate -S`                       |
| `-e`                 | Solo muestra archivos que existen actualmente              | `locate -e archivo_eliminado.txt` |
| `-q`                 | Modo silencioso (suprime mensajes de error)                | `locate -q nombre_parcial`        |
| `-r`, `--regexp`     | Usa expresión regular básica para buscar                   | `locate -r '\.conf$'`             |
| `--regex`            | Usa expresión regular extendida (como `egrep`)             | `locate --regex '\.(jpg\|png)$'`  |
| `-w`                 | Busca solo en rutas completas (no en nombres de archivo)   | `locate -w '*/bin/*'`             |
| `-A`, `--all`        | Muestra solo entradas que coinciden con TODOS los patrones | `locate -A informe .pdf`          |
| `-0`                 | Separa resultados con carácter nulo (útil para `xargs -0`) | `locate -0 *.tmp`                 |
