sed (stream editor) es un editor de texto no interactivo que procesa la entrada línea a línea aplicando un conjunto de comandos de edición. Lee de stdin o ficheros, aplica las transformaciones y escribe el resultado en stdout. Es fundamental en pipelines para sustitución, eliminación, inserción y transformación de texto.

```
sed [opciones] 'script' [archivo...]
sed [opciones] -f script.sed [archivo...]
```

---

## Opciones de línea de comandos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n` / `--quiet` / `--silent` | Suprime la salida automática de cada línea. Solo imprime con el comando `p`. | `sed -n '/error/p' app.log` |
| `-e <script>` / `--expression=<script>` | Añade un script a ejecutar. Permite múltiples expresiones. | `sed -e 's/foo/bar/' -e 's/baz/qux/' file` |
| `-f <archivo>` / `--file=<archivo>` | Lee el script desde un archivo. | `sed -f transformaciones.sed datos.txt` |
| `-i[SUFIJO]` / `--in-place[=SUFIJO]` | Edita el archivo en su lugar. Si se especifica sufijo, crea backup. | `sed -i.bak 's/old/new/g' file.txt` |
| `-E` / `-r` / `--regexp-extended` | Usa expresiones regulares extendidas (ERE), sin escapar `+`, `?`, `(`, `)`, `\|`. | `sed -E 's/[0-9]+/NUM/g' file` |
| `-z` / `--null-data` | Usa NUL en vez de newline como separador de registros (útil con `find -print0`). | `find . -print0 \| sed -z 's/old/new/g'` |
| `--posix` | Deshabilita extensiones GNU, modo POSIX estricto. | `sed --posix 's/a/b/' file` |
| `--sandbox` | Modo sandbox: deshabilita comandos `e`, `r`, `w`. | `sed --sandbox 's/a/b/' file` |
| `--debug` | Muestra la ejecución del programa paso a paso. | `sed --debug 's/a/b/' file` |

---

## Especificación de dirección (address)

La dirección precede al comando y determina qué líneas se procesan.

| Sintaxis | Descripción | Ejemplo |
|----------|-------------|---------|
| `N` | Número de línea exacto. | `sed '5d' file` (elimina línea 5) |
| `$` | Última línea del fichero. | `sed '$d' file` |
| `first~step` | Cada `step` líneas a partir de `first` (GNU). | `sed '1~2p' file` (líneas impares) |
| `0,/regex/` | Desde la línea 0 hasta la primera coincidencia (GNU). | `sed '0,/stop/d' file` |
| `addr1,addr2` | Rango de líneas (inclusive). | `sed '2,5d' file` |
| `/regex/` | Líneas que coincidan con la expresión regular. | `sed '/^#/d' file` |
| `/regex1/,/regex2/` | Desde la primera coincidencia de regex1 hasta regex2. | `sed '/START/,/END/d' file` |
| `addr1,+N` | Desde `addr1` más las siguientes `N` líneas. | `sed '/error/,+2p' file` |
| `addr1,~N` | Desde `addr1` hasta el múltiplo de `N` siguiente. | `sed '1,~5p' file` |
| `!` (sufijo) | Niega la dirección: aplica el comando a las líneas que NO coinciden. | `sed '/^#/!d' file` (conserva solo comentarios) |

---

## Comando `s` — sustitución

```
s/regex/reemplazo/flags
```

| Flag | Descripción | Ejemplo |
|------|-------------|---------|
| `g` | Sustituye todas las ocurrencias en la línea (global). | `sed 's/a/b/g' file` |
| `<n>` | Sustituye solo la n-ésima ocurrencia. | `sed 's/a/b/2' file` |
| `p` | Imprime la línea si se produjo sustitución (útil con `-n`). | `sed -n 's/old/new/p' file` |
| `i` / `I` | Coincidencia insensible a mayúsculas (GNU). | `sed 's/error/ERROR/gI' file` |
| `e` | Ejecuta el resultado de la sustitución como comando shell (GNU). | `sed 's/.*/echo &/e' cmds.txt` |
| `w <archivo>` | Escribe las líneas modificadas en un archivo. | `sed 's/foo/bar/w output.txt' file` |
| `m` / `M` | Modo multilínea: `^` y `$` coinciden con inicio/fin de cada línea del buffer. | `sed 'N;s/\n/ /m' file` |

**Referencias en el reemplazo:**

| Sintaxis | Descripción | Ejemplo |
|----------|-------------|---------|
| `&` | Toda la cadena coincidente. | `sed 's/[0-9]*/[&]/g' file` |
| `\1` .. `\9` | Grupos de captura (backreferences). | `sed 's/\(foo\)\(bar\)/\2\1/' file` |
| `\u` | Convierte el siguiente carácter a mayúscula. | `sed 's/\b\(.\)/\u\1/g' file` |
| `\l` | Convierte el siguiente carácter a minúscula. | `sed 's/ERROR/\l&/g' file` |
| `\U` | Convierte el resto del reemplazo a mayúsculas. | `sed 's/.*/\U&/' file` |
| `\L` | Convierte el resto del reemplazo a minúsculas. | `sed 's/.*/\L&/' file` |
| `\E` | Termina la conversión iniciada por `\U` o `\L`. | `sed 's/\(foo\)/\U\1\E bar/' file` |

---

## Comandos de impresión y listado

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `p` | Imprime la línea actual (o patrón space). | `sed -n '1,5p' file` |
| `P` | Imprime hasta el primer newline del patrón space (multilínea). | `sed -n 'N;P' file` |
| `l` | Muestra la línea con caracteres no imprimibles visibles y ajuste de línea. | `sed -n 'l' file` |
| `l <n>` | Como `l` pero ajusta a `n` caracteres de ancho. | `sed -n 'l 80' file` |
| `=` | Imprime el número de la línea actual. | `sed -n '/error/=' file` |

---

## Comandos de eliminación

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `d` | Elimina la línea actual (pattern space) y comienza el siguiente ciclo. | `sed '/^#/d' file` |
| `D` | Elimina hasta el primer newline del patrón space (multilínea) y reinicia el ciclo sin leer nueva línea. | `sed 'N;/\n$/D' file` |

---

## Comandos de inserción y adición

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `a <texto>` / `a\<texto>` | Añade texto después de la línea actual. | `sed '/^end$/a\nueva línea' file` |
| `i <texto>` / `i\<texto>` | Inserta texto antes de la línea actual. | `sed '1i\# Cabecera' file` |
| `c <texto>` / `c\<texto>` | Reemplaza la línea actual por el texto indicado. | `sed '/PASSWORD/c\[REDACTED]' config` |

---

## Comandos de transformación

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `y/lista1/lista2/` | Transliteración carácter a carácter (equivalente a `tr`). | `sed 'y/abc/ABC/' file` |
| `q [código]` | Sale inmediatamente, imprimiendo la línea actual. Código de salida opcional. | `sed '10q' file` (imprime las 10 primeras líneas) |
| `Q [código]` | Sale sin imprimir la línea actual. | `sed '10Q' file` |

---

## Comandos de fichero

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `r <archivo>` | Lee el contenido de un archivo y lo inserta después de la línea actual. | `sed '/EOF/r footer.txt' file` |
| `R <archivo>` | Lee una sola línea del archivo y la inserta. | `sed '1R header.txt' file` |
| `w <archivo>` | Escribe la línea actual en el archivo (lo crea o sobreescribe). | `sed -n '/error/w errores.log' file` |
| `W <archivo>` | Escribe hasta el primer newline del patrón space en el archivo. | `sed -n 'N;W output.txt' file` |
| `e [cmd]` | Si no hay argumento, ejecuta el patrón space como comando. Con argumento, ejecuta el comando e inserta la salida. | `sed 's/.*/ls &/e' dirs.txt` |

---

## Control de flujo y buffers

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `n` | Imprime la línea actual (si no hay `-n`) y carga la siguiente. | `sed '/START/{n;d}' file` |
| `N` | Añade la siguiente línea al patrón space (separadas por `\n`). Útil para procesar pares de líneas. | `sed 'N;s/\n/ /' file` (une pares de líneas) |
| `h` | Copia el patrón space al hold space. | `sed -n '1h;2{H;g;p}' file` |
| `H` | Añade el patrón space al hold space (con `\n` separador). | `sed 'H;${g;s/\n/ /g;p}' file` |
| `g` | Copia el hold space al patrón space. | `sed -n 'g;p' file` |
| `G` | Añade el hold space al patrón space (con `\n` separador). | `sed 'G' file` (duplica cada línea) |
| `x` | Intercambia patrón space y hold space. | `sed -n 'x;2p' file` |
| `b [etiqueta]` | Salta a la etiqueta. Sin etiqueta, salta al final del script. | `sed '/ok/b; s/error/ERROR/' file` |
| `t [etiqueta]` | Salta a la etiqueta si se realizó una sustitución `s` exitosa desde el último test. | `sed ':loop; s/aa/a/; t loop' file` |
| `T [etiqueta]` | Salta a la etiqueta si NO se realizó ninguna sustitución (GNU). | `sed 's/a/b/; T end; s/c/d/; :end' file` |
| `: etiqueta` | Define una etiqueta para `b` y `t`. | `sed ':start; s/  / /; t start' file` |
| `{ }` | Agrupa comandos que se ejecutan para una dirección. | `sed '/header/{n;s/old/new/}' file` |

---

## Expresiones regulares: BRE vs ERE

Por defecto sed usa **BRE** (Basic Regular Expressions); con `-E`/`-r` usa **ERE** (Extended). La diferencia clave: en BRE los metacaracteres `+ ? { } ( ) |` son literales y hay que escaparlos con `\` para darles significado especial; en ERE funcionan directamente.

| Construcción | BRE (por defecto) | ERE (`-E`) | Significado |
|--------------|-------------------|------------|-------------|
| Agrupación | `\( \)` | `( )` | Grupo / captura. |
| Alternancia | `\|` | `\|` (literal `|`) | "o" lógico. |
| Una o más | `\+` | `+` | Repetición ≥ 1. |
| Cero o una | `\?` | `?` | Opcional. |
| Cantidad exacta | `\{n,m\}` | `{n,m}` | Entre n y m repeticiones. |
| Backreference | `\1`–`\9` | `\1`–`\9` | Referencia a grupo (en ambos). |

| Metacarácter | Función | Ejemplo |
|--------------|---------|---------|
| `.` | Cualquier carácter (no newline). | `s/a.c/X/` |
| `*` | Cero o más del átomo previo. | `s/ab*/X/` |
| `^` / `$` | Inicio / fin de línea. | `s/^#//`, `s/ *$//` |
| `[...]` / `[^...]` | Clase de caracteres / negada. | `s/[0-9]//g` |
| `[[:class:]]` | Clases POSIX (`alpha`, `digit`, `space`, `alnum`, `punct`...). | `s/[[:space:]]//g` |
| `\<` / `\>` | Límite de palabra inicio / fin (GNU). | `s/\<foo\>/bar/` |
| `\b` / `\B` | Límite / no-límite de palabra (GNU). | `s/\bfoo\b/bar/` |
| `\w` / `\W` | Carácter de palabra / no-palabra (GNU). | `s/\w\+/X/` |
| `\s` / `\S` | Espacio / no-espacio (GNU). | `s/\s\+/ /g` |
| `\+` `\?` `\|` | (Solo en BRE) versiones escapadas de los operadores ERE. | `sed 's/colou\?r/X/'` |

---

## Casos de uso comunes

```bash
# Sustitución global en fichero (in-place con backup)
sed -i.bak 's/http:/https:/g' config.txt

# Eliminar comentarios y líneas vacías
sed '/^#/d; /^$/d' config.conf

# Imprimir solo líneas entre dos patrones (inclusive)
sed -n '/START/,/END/p' file.txt

# Eliminar espacios al inicio y final de cada línea
sed 's/^[[:space:]]*//; s/[[:space:]]*$//' file.txt

# Extraer IPs de un fichero de log
grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' access.log | sed 's/^/IP: /'

# Numerar líneas (alternativa a nl)
sed '=' file.txt | sed 'N; s/\n/\t/'

# Unir líneas que terminan en backslash
sed ':a; /\\$/N; s/\\\n//; ta' makefile

# Reemplazar la 3ª ocurrencia en cada línea
sed 's/foo/bar/3' file.txt

# Insertar línea después de la primera coincidencia
sed '0,/pattern/!{/pattern/a\nueva línea'}' file.txt
```
