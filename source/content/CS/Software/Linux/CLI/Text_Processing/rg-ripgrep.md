ripgrep (`rg`) es una herramienta de búsqueda de texto recursiva de alto rendimiento escrita en Rust. Combina la velocidad del motor de expresiones regulares de Rust con el respeto automático de `.gitignore`, `.ignore` y `.rgignore`, y soporte para múltiples encodings. Es significativamente más rápido que `grep`, `ag` o `ack` en la mayoría de casos gracias al uso de SIMD, búsqueda paralelizada y evasión de directorios ocultos/binarios por defecto. Soporta la sintaxis regex de Rust (RE2-compatible, sin backtracking).

```
rg [opciones] <PATRÓN> [ruta...]
rg [opciones] -e <PATRÓN> ... [ruta...]
rg [opciones] -f <fichero_patrones> ... [ruta...]
rg [opciones] --files [ruta...]
rg [opciones] --type-list
```

---

## Patrón y expresiones regulares

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `<PATRÓN>` | Expresión regular (sintaxis Rust/RE2) a buscar. Por defecto: búsqueda en el directorio actual recursiva. | `rg "fn main"` |
| `-e <PATRÓN>` / `--regexp=<PATRÓN>` | Especifica el patrón explícitamente. Permite múltiples `-e` para combinar con OR. | `rg -e "TODO" -e "FIXME"` |
| `-f <fichero>` / `--file=<fichero>` | Lee patrones desde un fichero (uno por línea). | `rg -f patrones.txt` |
| `-F` / `--fixed-strings` | Trata el patrón como cadena literal, sin interpretar metacaracteres regex. | `rg -F "a.b.c"` |
| `-x` / `--line-regexp` | Fuerza que el patrón coincida con la línea completa (ancla `^...$` implícita). | `rg -x "exacta"` |
| `-w` / `--word-regexp` | El patrón debe coincidir como palabra completa (delimitada por `\b`). | `rg -w "error"` |
| `-P` / `--pcre2` | Usa el motor PCRE2 en lugar del motor Rust. Necesario para lookahead/lookbehind y backreferences. | `rg -P "(?<=foo)bar"` |
| `--pcre2-version` | Muestra la versión de PCRE2 integrada. | `rg --pcre2-version` |
| `-U` / `--multiline` | Permite que el patrón coincida en múltiples líneas. El `.` no coincide con `\n` por defecto. | `rg -U "inicio.*fin"` |
| `--multiline-dotall` | Con `-U`, el `.` también coincide con `\n`. | `rg -U --multiline-dotall "inicio.*fin"` |
| `--crlf` | Trata `\r\n` como fin de línea (ficheros Windows). | `rg --crlf "patrón"` |

---

## Sensibilidad a mayúsculas

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` / `--ignore-case` | Búsqueda insensible a mayúsculas/minúsculas. | `rg -i "error"` |
| `-s` / `--case-sensitive` | Fuerza sensibilidad (anula `-i` si estuviera en config). | `rg -s "Error"` |
| `-S` / `--smart-case` | Insensible si el patrón es todo minúsculas; sensible si contiene alguna mayúscula. Por defecto. | `rg -S "Error"` |

---

## Selección de ficheros y rutas

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-g <glob>` / `--glob=<glob>` | Incluye/excluye ficheros según patrón glob. Prefijo `!` para excluir. | `rg "TODO" -g "*.rs"` / `rg "x" -g "!*.log"` |
| `-t <tipo>` / `--type=<tipo>` | Busca solo en ficheros del tipo indicado (ver `rg --type-list`). | `rg "import" -t py` |
| `-T <tipo>` / `--type-not=<tipo>` | Excluye ficheros del tipo indicado. | `rg "fn" -T json` |
| `--type-add=<def>` | Define un tipo de fichero personalizado. Formato: `nombre:glob`. | `rg "error" --type-add="log:*.log" -t log` |
| `--type-clear=<tipo>` | Elimina la definición de un tipo. | `rg --type-clear=py "algo"` |
| `--type-list` | Lista todos los tipos de fichero conocidos con sus extensiones. | `rg --type-list` |
| `-l` / `--files-with-matches` | Imprime solo los nombres de ficheros con al menos una coincidencia. | `rg -l "TODO"` |
| `-L` / `--files-without-match` | Imprime solo los nombres de ficheros sin ninguna coincidencia. | `rg -L "copyright"` |
| `--files` | Lista todos los ficheros que serían buscados (sin buscar). | `rg --files src/` |
| `--iglob=<glob>` | Como `-g` pero insensible a mayúsculas en el glob. | `rg "x" --iglob "*.PY"` |

---

## Recursión y ficheros ignorados

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| (por defecto) | Respeta automáticamente `.gitignore`, `.ignore`, `.rgignore` y no sigue symlinks. | — |
| `-u` / `--unrestricted` | Reduce las restricciones: 1× ignora `.gitignore`, 2× también busca en ocultos, 3× también busca en binarios. | `rg -uuu "patrón"` |
| `--no-ignore` | No respeta ningún fichero de ignore (`.gitignore`, `.ignore`, etc.). | `rg --no-ignore "patrón"` |
| `--no-ignore-vcs` | No respeta `.gitignore` pero sí `.ignore` y `.rgignore`. | `rg --no-ignore-vcs "patrón"` |
| `--no-ignore-global` | No respeta los ficheros de ignore globales. | `rg --no-ignore-global "patrón"` |
| `--ignore-file=<fichero>` | Usa el fichero de ignore indicado adicionalmente. | `rg --ignore-file=.customignore "patrón"` |
| `--hidden` / `-.` | Busca también en ficheros y directorios ocultos (que empiezan por `.`). | `rg --hidden "patrón"` |
| `--follow` / `-L` (alias) | Sigue enlaces simbólicos al buscar recursivamente. | `rg --follow "patrón"` |
| `--max-depth=<N>` / `-d <N>` | Limita la profundidad de recursión a N niveles. | `rg --max-depth=2 "patrón"` |
| `--one-file-system` | No cruza límites de sistemas de ficheros. | `rg --one-file-system "patrón" /` |
| `--no-follow` | No sigue symlinks (por defecto). | — |

---

## Contexto y salida de coincidencias

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n` / `--line-number` | Muestra el número de línea de cada coincidencia (por defecto cuando hay TTY). | `rg -n "error"` |
| `-N` / `--no-line-number` | Suprime los números de línea. | `rg -N "error"` |
| `-c` / `--count` | Muestra el número de coincidencias por fichero. | `rg -c "TODO" src/` |
| `--count-matches` | Como `-c` pero cuenta cada coincidencia individual (no cada línea). | `rg --count-matches "TODO"` |
| `-o` / `--only-matching` | Imprime solo la parte de la línea que coincide con el patrón. | `rg -o "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}"` |
| `-A <N>` / `--after-context=<N>` | Muestra N líneas después de cada coincidencia. | `rg -A 3 "error"` |
| `-B <N>` / `--before-context=<N>` | Muestra N líneas antes de cada coincidencia. | `rg -B 2 "panic"` |
| `-C <N>` / `--context=<N>` | Muestra N líneas antes y después. | `rg -C 3 "exception"` |
| `--context-separator=<sep>` | Separador entre grupos de contexto. Por defecto: `--`. | `rg -C 2 --context-separator="===" "fn"` |
| `--no-context-separator` | Suprime el separador entre grupos. | `rg -C 2 --no-context-separator "error"` |
| `-m <N>` / `--max-count=<N>` | Máximo N coincidencias por fichero. | `rg -m 1 "TODO"` |
| `--max-filesize=<N>` | No busca en ficheros mayores de N bytes. | `rg --max-filesize=1M "patrón"` |

---

## Formato de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--color=<never\|auto\|always\|ansi>` | Control de colores en la salida. | `rg --color=always "error" \| less -R` |
| `--colors=<spec>` | Personaliza el color de un elemento específico. Formato: `tipo:attr:valor`. | `rg --colors="match:fg:red" "error"` |
| `--heading` / `--no-heading` | Muestra (o no) el nombre del fichero como cabecera separada de las coincidencias. | `rg --heading "fn"` |
| `--column` | Muestra el número de columna de cada coincidencia. | `rg --column "error"` |
| `-H` / `--with-filename` | Muestra el nombre de fichero en cada línea (por defecto al buscar en múltiples). | `rg -H "error"` |
| `--no-filename` | Suprime el nombre de fichero en la salida. | `rg --no-filename "error"` |
| `-I` / `--no-filename` (alias) | Equivale a `--no-filename`. | `rg -I "patrón" fichero.txt` |
| `--trim` | Elimina espacios en blanco iniciales de cada línea de la salida. | `rg --trim "error"` |
| `--field-match-separator=<sep>` | Separador entre campos en la salida (número de línea, columna, contenido). Por defecto: `:`. | `rg --field-match-separator="\t" "error"` |
| `-0` / `--null` | Usa NUL (`\0`) como separador en lugar de newline en la salida de ficheros (con `-l`). Compatible con `xargs -0`. | `rg -l0 "TODO" \| xargs -0 grep` |
| `--null-data` | Usa NUL como delimitador de línea en la entrada. | `rg --null-data "patrón"` |
| `--path-separator=<sep>` | Separador de rutas en la salida (útil en Windows). | `rg --path-separator="/" "error"` |
| `-r <reemplazo>` / `--replace=<reemplazo>` | Imprime el texto con las coincidencias reemplazadas. No modifica ficheros; solo salida. Acepta `$1`, `$2`... para grupos de captura. | `rg "(\w+)@(\w+)" -r "$1 at $2"` |

---

## Búsqueda de ficheros y estadísticas

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--stats` | Muestra estadísticas al final: ficheros buscados, ficheros con coincidencias, líneas totales, tiempo. | `rg --stats "error"` |
| `--files` | Lista ficheros que serían buscados sin ejecutar la búsqueda. | `rg --files src/` |
| `--count` / `-c` | Número de líneas con coincidencia por fichero. | `rg -c "fn" src/` |

---

## Encodings y binarios

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-E <encoding>` / `--encoding=<encoding>` | Especifica el encoding del texto de entrada. Por defecto: auto-detect. | `rg -E utf-16 "patrón" fichero.txt` |
| `--text` / `-a` | Busca también en ficheros binarios tratándolos como texto. | `rg -a "ELF" binario` |
| `--binary` | Busca en binarios mostrando offset de la coincidencia. | `rg --binary "HTTP"` |
| `--no-binary` | No busca en ficheros binarios (por defecto). | — |

---

## Rendimiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-j <N>` / `--threads=<N>` | Número de hilos de búsqueda. Por defecto: número de CPUs lógicas. | `rg -j 4 "patrón"` |
| `--mmap` / `--no-mmap` | Usa/no usa mmap para leer ficheros. `--no-mmap` reduce uso de memoria en ficheros grandes. | `rg --mmap "patrón"` |
| `--dfa-size-limit=<N>` | Tamaño máximo del DFA compilado en bytes. Relevante para regex muy complejos. | `rg --dfa-size-limit=500M "patrón"` |
| `--regex-size-limit=<N>` | Tamaño máximo de la memoria usada por el compilador de regex. | `rg --regex-size-limit=100M "patrón"` |

---

## Casos de uso comunes

```bash
# Búsqueda básica recursiva
rg "error" src/

# Búsqueda insensible a mayúsculas
rg -i "warning"

# Solo en ficheros Python
rg "import" -t py

# Buscar como literal (sin regex)
rg -F "a.b.c"

# Con contexto (3 líneas antes y después)
rg -C 3 "panic" src/

# Solo nombres de ficheros con coincidencias
rg -l "TODO" .

# Ficheros sin copyright
rg -L "Copyright"

# Buscar en ocultos e ignorados
rg --hidden --no-ignore "SECRET"

# Extraer todas las IPs de logs
rg -o '\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b' /var/log/

# Reemplazar en salida (no modifica fichero)
rg "old_func" -r "new_func" src/

# Buscar en binarios
rg -a "HTTP" binario

# Contar coincidencias por fichero
rg -c "fn " src/*.rs

# Búsqueda multilinea (PCRE2)
rg -U -P "class \w+.*?\{" --multiline-dotall *.py

# Integrar con fzf para búsqueda interactiva
rg --line-number "" | fzf --delimiter=: --preview 'bat --highlight-line {2} {1}'
```
