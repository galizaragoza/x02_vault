
| **Parámetro**                  | **Función**                                                | **Ejemplo de sintaxis**                           |
| ------------------------------ | ---------------------------------------------------------- | ------------------------------------------------- |
| `egrep`                        | Equivalente a `grep -E` (expresiones regulares extendidas) |                                                   |
| `fgrep`                        | Equivalente a `grep -F` (búsqueda literal rápida)          |                                                   |
| `rgrep`                        | Búsqueda recursiva en directorios                          |                                                   |
| `zgrep`                        | Para archivos comprimidos gzip                             |                                                   |
| `bzgrep`                       | Para archivos comprimidos bzip2                            |                                                   |
| `-i`                           | Ignorar mayúsculas/minúsculas                              | `grep -i "password" config.php`                   |
| `-f`                           | Recibe pars de un archivo                                  | `grep -f file.txt`                                |
| `-F` o `--fixed-strings`       | Busca cadenas literales (no regex)                         | `grep -F "texto.exacto" archivo.txt`              |
| `-o`                           | Muestra solo la parte que coincide                         | `grep -o "http://[^ ]*" archivo.html`             |
| `-r` / `-R`                    | Recursivo en directorios                                   | `grep -r "DB_PASSWORD" /var/www`                  |
| `-E`                           | Regex extendida (sin escapar)                              | `grep -E "[0-9]{4}-[0-9]{4}-[0-9]{4}-[0-9]{4}" *` |
| `-P`                           | Regex Perl (lookarounds, \d, etc.)                         | `grep -P '(?<=api_key=)[A-Za-z0-9]{32}' files`    |
| `-w`                           | Palabra completa                                           | `grep -w "admin" users.txt`                       |
| `-n`                           | Mostrar número de línea                                    | `grep -n "root:" /etc/passwd`                     |
| `-c`                           | Contar coincidencias                                       | `grep -c "error" logs/*.log`                      |
| `-l`                           | Solo nombres de archivos con match                         | `grep -l "ssh-rsa" ~/.ssh/*`                      |
| `-L` o `--files-without-match` | Muestra archivos sin coincidencias                         | `grep -L "patron" *.txt`                          |
| `-v`                           | Invertir (líneas que NO coinciden)                         | `grep -v "^#" config.conf`                        |
| `-A N`                         | Mostrar N líneas después                                   | `grep -A 3 "password" app.js`                     |
| `-B N`                         | Mostrar N líneas antes                                     | `grep -B 2 "error" server.log`                    |
| `-C N`                         | Mostrar N líneas antes y después                           | `grep -C 5 "login failed" auth.log`               |
| `--color=always`               | Forzar color (útil con pipes)                              | `grep --color=always "ERROR" logs                 |
| `-a`                           | Tratar binarios como texto                                 | `grep -a "password" binary.exe`                   |
| `-z`                           | Archivos terminados en NUL (para xargs -0)                 | `find . -type f -print0                           |
