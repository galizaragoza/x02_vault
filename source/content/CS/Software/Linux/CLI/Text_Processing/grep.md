grep (Global Regular Expression Print) busca en cada fichero de entrada (o en la entrada estándar) las líneas que casan con un patrón y, por defecto, las imprime. El patrón se interpreta como expresión regular básica (BRE) salvo que se cambie el sabor con `-E`, `-F`, `-G` o `-P`. Devuelve estado de salida `0` si hubo al menos una coincidencia, `1` si ninguna y `>1` ante error, lo que lo hace ideal para condicionales en scripts. Es la herramienta canónica de filtrado de texto en UNIX y la base de las variantes `egrep`, `fgrep`, `rgrep`, `zgrep`, etc.

```
grep [opciones] PATRÓN [fichero...]
grep [opciones] -e PATRÓN... | -f FICHERO... [fichero...]
```

---

## Variantes y enlaces

Históricamente comandos separados; en GNU grep son envoltorios (`egrep`/`fgrep` están **deprecados**, emiten aviso, usar las flags).

| **Variante** | **Función**                                          | **Equivalente / Ejemplo**        |
| ------------ | ---------------------------------------------------- | -------------------------------- |
| `egrep`      | Regex extendida.                                     | `grep -E` → ver [[egrep]]        |
| `fgrep`      | Cadenas literales, sin regex (rápido).               | `grep -F "1.2.3.4" hosts`        |
| `rgrep`      | Búsqueda recursiva.                                  | `grep -r`                        |
| `zgrep`      | Busca en ficheros comprimidos gzip.                  | `zgrep "error" log.gz`           |
| `zegrep` / `zfgrep` | Combinan compresión gzip con `-E` / `-F`.     | `zegrep "warn|err" log.gz`       |
| `bzgrep` / `xzgrep` / `lzgrep` | Equivalentes para bzip2 / xz / lzma.   | `xzgrep "panic" kern.log.xz`     |

---

## Selección del sabor de regex

| **Flag**                          | **Función**                                                              | **Ejemplo**                                       |
| --------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| `-G` / `--basic-regexp`           | Regex básica (BRE). Por defecto; `+ ? { } ( ) \|` deben escaparse.       | `grep -G 'colou\?r' file` (literal en BRE)        |
| `-E` / `--extended-regexp`        | Regex extendida (ERE): `+ ? { } ( ) \|` sin escapar.                     | `grep -E '[0-9]{1,3}(\.[0-9]{1,3}){3}' hosts`     |
| `-F` / `--fixed-strings`          | Cadenas literales separadas por saltos de línea, sin metacaracteres.     | `grep -F '$HOME/.ssh' config`                     |
| `-P` / `--perl-regexp`            | Regex Perl (PCRE): lookarounds, `\d`, `\b`, no-greedy, `\K`.             | `grep -P '(?<=api_key=)[A-Za-z0-9]{32}' .env`     |

---

## Control de coincidencia

| **Flag**                          | **Función**                                                              | **Ejemplo**                                       |
| --------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| `-e PATRÓN` / `--regexp=PATRÓN`   | Especifica patrón; permite varios y patrones que empiezan por `-`.       | `grep -e 'error' -e 'fail' app.log`               |
| `-f FICHERO` / `--file=FICHERO`   | Lee patrones (uno por línea) de un fichero. `-` para stdin.              | `grep -f patrones.txt acceso.log`                 |
| `-i` / `--ignore-case`            | Ignora mayúsculas/minúsculas.                                            | `grep -i "password" config.php`                   |
| `--no-ignore-case`                | Fuerza distinción de mayúsculas (anula un `-i` previo).                  | `grep -i pat a; grep --no-ignore-case pat b`      |
| `-v` / `--invert-match`           | Selecciona las líneas que NO casan.                                      | `grep -v "^#" config.conf`                        |
| `-w` / `--word-regexp`            | Casa solo palabras completas (límites de palabra).                       | `grep -w "admin" users.txt`                       |
| `-x` / `--line-regexp`            | El patrón debe casar la línea entera.                                    | `grep -x "root:x:0:0" /etc/passwd`                |
| `-y`                              | Obsoleto; alias de `-i`.                                                  | `grep -y "ok" file`                               |

---

## Control general de salida

| **Flag**                          | **Función**                                                              | **Ejemplo**                                       |
| --------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| `-c` / `--count`                  | Cuenta líneas coincidentes por fichero (no las imprime).                 | `grep -c "error" logs/*.log`                      |
| `-m NUM` / `--max-count=NUM`      | Para tras NUM coincidencias por fichero.                                 | `grep -m 1 "BEGIN" dump.sql`                      |
| `-o` / `--only-matching`          | Imprime solo la porción que casa, una por línea.                         | `grep -o 'https\?://[^ ]*' index.html`            |
| `-l` / `--files-with-matches`     | Solo nombres de ficheros con al menos una coincidencia.                  | `grep -rl "ssh-rsa" ~/.ssh/`                      |
| `-L` / `--files-without-match`    | Solo nombres de ficheros SIN coincidencias.                              | `grep -L "GPLv3" *.c`                             |
| `-q` / `--quiet` / `--silent`     | No imprime nada; sale en cuanto hay coincidencia (usar el estado).       | `grep -q "ok" f && echo presente`                 |
| `-s` / `--no-messages`            | Suprime errores de ficheros inexistentes o ilegibles.                    | `grep -s "x" /no/existe`                          |
| `--color[=WHEN]` / `--colour`     | Resalta lo coincidente. `WHEN` = `auto`/`always`/`never`.                | `grep --color=always "ERROR" log \| less -R`      |

---

## Prefijo de línea y formato

| **Flag**                          | **Función**                                                              | **Ejemplo**                                       |
| --------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| `-n` / `--line-number`            | Antepone el número de línea.                                             | `grep -n "root:" /etc/passwd`                     |
| `-b` / `--byte-offset`            | Antepone el desplazamiento en bytes de la coincidencia.                  | `grep -bo "PK" archivo.zip`                       |
| `-H` / `--with-filename`          | Antepone el nombre de fichero (por defecto con varios ficheros).         | `grep -H "TODO" main.c`                           |
| `-h` / `--no-filename`            | Nunca antepone el nombre de fichero.                                     | `grep -h "ip" *.log`                              |
| `--label=ETIQUETA`                | Etiqueta a usar como nombre de fichero para la entrada estándar.         | `cat f \| grep --label=in -H x`                   |
| `-T` / `--initial-tab`            | Alinea con tabulador el contenido tras los prefijos.                     | `grep -nT "def " app.py`                          |
| `-Z` / `--null`                   | Termina el nombre de fichero con NUL (canalizar a `xargs -0`).           | `grep -rlZ "secret" . \| xargs -0 rm`             |
| `-u` / `--unix-byte-offsets`      | Reporta offsets como si no hubiera CR (Windows/MS-DOS). Solo con `-b`.   | `grep -bu "x" archivo.txt`                        |

---

## Control de contexto

| **Flag**                          | **Función**                                                              | **Ejemplo**                                       |
| --------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| `-A NUM` / `--after-context=NUM`  | Muestra NUM líneas después de cada coincidencia.                         | `grep -A 3 "password" app.js`                     |
| `-B NUM` / `--before-context=NUM` | Muestra NUM líneas antes de cada coincidencia.                           | `grep -B 2 "error" server.log`                    |
| `-C NUM` / `--context=NUM` / `-NUM` | Muestra NUM líneas antes y después.                                    | `grep -C 5 "login failed" auth.log`               |
| `--group-separator=SEP`           | Cadena entre grupos de contexto no contiguos (por defecto `--`).         | `grep -A1 --group-separator='===' x f`            |
| `--no-group-separator`            | Suprime el separador entre grupos de contexto.                           | `grep -A1 --no-group-separator x f`               |

---

## Selección de ficheros y directorios

| **Flag**                          | **Función**                                                              | **Ejemplo**                                       |
| --------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| `-r` / `--recursive`              | Recursivo; sigue enlaces simbólicos solo si están en línea de comandos.  | `grep -r "DB_PASSWORD" /var/www`                  |
| `-R` / `--dereference-recursive`  | Recursivo siguiendo TODOS los enlaces simbólicos.                        | `grep -R "key" /etc/`                             |
| `--include=GLOB`                  | En recursivo, solo ficheros cuyo nombre case el glob.                    | `grep -r --include='*.py' "import os" .`          |
| `--exclude=GLOB`                  | Omite ficheros cuyo nombre case el glob.                                 | `grep -r --exclude='*.min.js' "eval" .`           |
| `--exclude-from=FICHERO`          | Lee del fichero los globs de exclusión (uno por línea).                  | `grep -r --exclude-from=.gitignore "x" .`         |
| `--exclude-dir=GLOB`              | Omite directorios cuyo nombre case el glob.                              | `grep -r --exclude-dir='.git' "TODO" .`           |
| `-d ACCIÓN` / `--directories=ACCIÓN` | Qué hacer con directorios: `read`, `skip`, `recurse`.                 | `grep -d skip "x" *`                              |
| `-D ACCIÓN` / `--devices=ACCIÓN`  | Qué hacer con devices/FIFOs/sockets: `read` o `skip`.                    | `grep -D skip "x" /dev/* `                        |

---

## Tratamiento de binarios y datos

| **Flag**                          | **Función**                                                              | **Ejemplo**                                       |
| --------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| `-a` / `--text`                   | Trata los ficheros binarios como texto.                                  | `grep -a "password" binario.exe`                  |
| `-I`                              | Ignora ficheros binarios (los trata como sin coincidencias).             | `grep -rI "cadena" .`                             |
| `--binary-files=TIPO`             | `binary` (def.), `text` (=`-a`) o `without-match` (=`-I`).               | `grep --binary-files=text "x" core`              |
| `-U` / `--binary`                 | Trata el fichero como binario (no elimina CR en Windows).                | `grep -U "x" archivo.bin`                         |
| `-z` / `--null-data`              | Las líneas terminan en NUL en vez de salto de línea.                     | `find . -print0 \| grep -z "tmp"`                 |

---

## Rendimiento y misceláneo

| **Flag**                          | **Función**                                                              | **Ejemplo**                                       |
| --------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------- |
| `--line-buffered`                 | Vacía la salida línea a línea (útil al canalizar `tail -f`).             | `tail -f log \| grep --line-buffered ERR`         |
| `-V` / `--version`                | Muestra versión y sale.                                                  | `grep -V`                                         |
| `--help`                          | Muestra ayuda y sale.                                                    | `grep --help`                                     |

> `--mmap` existió para mapear ficheros en memoria; está obsoleto/ignorado en GNU grep moderno.

---

## Estado de salida

| Código | Significado                                  |
| ------ | -------------------------------------------- |
| `0`    | Se encontró al menos una coincidencia.       |
| `1`    | Ninguna coincidencia.                        |
| `>1`   | Error (fichero ausente, regex inválida, …). Con `-q`/`-s` el error de fichero puede no elevar el código. |

---

## Casos de uso comunes

```bash
# IPs en un log (ERE)
grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}' acceso.log | sort -u

# Buscar en código fuente, solo .py, con número de línea, sin entrar en .git
grep -rn --include='*.py' --exclude-dir='.git' "def main" .

# Líneas no comentadas ni vacías de un config
grep -vE '^\s*(#|$)' /etc/ssh/sshd_config

# Extraer valor con lookbehind PCRE
grep -Po '(?<="token":")[^"]+' respuesta.json

# Comprobar presencia en script (silencioso)
grep -q "^root:" /etc/passwd && echo "root existe"

# Borrar de forma segura ficheros que contienen un secreto
grep -rlZ "BEGIN PRIVATE KEY" . | xargs -0 rm -i

# Seguir un log en vivo filtrando, sin buffering
tail -f /var/log/syslog | grep --line-buffered -i 'error\|fail'
```

Relacionados: [[egrep]] · [[sed]] · [[awk]] · [[rg-ripgrep]] · [[cut]]
