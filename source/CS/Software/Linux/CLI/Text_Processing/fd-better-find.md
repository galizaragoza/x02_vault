fd es un reemplazo moderno de `find` escrito en Rust, con una sintaxis más intuitiva, búsqueda paralela, colores por tipo de fichero, respeto automático de `.gitignore` y `.fdignore`, y soporte para regex y patrones glob. En la mayoría de casos es significativamente más rápido que `find` gracias a la búsqueda paralela y al saltarse directorios ignorados. Por defecto la búsqueda es insensible a mayúsculas si el patrón es todo minúsculas (smart-case).

```
fd [opciones] [PATRÓN] [ruta...]
```

---

## Patrón de búsqueda

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `[PATRÓN]` | Expresión regular (por defecto) o glob que debe coincidir con el nombre del fichero. Sin patrón lista todo. | `fd ".py$"` |
| `-g` / `--glob` | Trata el patrón como glob en lugar de regex. | `fd -g "*.log"` |
| `-F` / `--fixed-strings` | Trata el patrón como cadena literal sin interpretar regex ni glob. | `fd -F "main.cpp"` |
| `-i` / `--ignore-case` | Búsqueda insensible a mayúsculas (anula smart-case). | `fd -i "readme"` |
| `-s` / `--case-sensitive` | Búsqueda sensible a mayúsculas (anula smart-case). | `fd -s "README"` |
| `--and=<patrón>` | El resultado debe coincidir también con este patrón adicional (AND lógico). | `fd "config" --and ".toml"` |
| `-p` / `--full-path` | El patrón se aplica a la ruta completa, no solo al nombre del fichero. | `fd -p "src/lib"` |

---

## Tipo de fichero

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t <tipo>` / `--type=<tipo>` | Filtra por tipo. Tipos: `f`/`file`, `d`/`directory`, `l`/`symlink`, `x`/`executable`, `e`/`empty`, `s`/`socket`, `p`/`pipe`, `b`/`block-device`, `c`/`char-device`. | `fd -t f "*.rs"` |
| `-t x` | Solo ejecutables. | `fd -t x` |
| `-t e` | Solo ficheros/directorios vacíos. | `fd -t e` |

---

## Extensión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-e <ext>` / `--extension=<ext>` | Filtra por extensión (sin punto). Se puede repetir para múltiples extensiones (OR). | `fd -e py -e pyi` |

---

## Profundidad y recursión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-d <N>` / `--max-depth=<N>` | Profundidad máxima de búsqueda. | `fd --max-depth=2 "*.conf"` |
| `--min-depth=<N>` | Profundidad mínima de búsqueda. | `fd --min-depth=2 "*.py"` |
| `--exact-depth=<N>` | Busca exactamente a esa profundidad. | `fd --exact-depth=3` |

---

## Ficheros ignorados y ocultos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-H` / `--hidden` | Incluye ficheros y directorios ocultos (`.nombre`). | `fd -H ".env"` |
| `-I` / `--no-ignore` | No respeta `.gitignore`, `.ignore`, `.fdignore`. | `fd -I "*.log"` |
| `--no-ignore-vcs` | No respeta `.gitignore` pero sí `.ignore`. | `fd --no-ignore-vcs "patrón"` |
| `--no-ignore-parent` | No hereda reglas de ignore de directorios padre. | `fd --no-ignore-parent "patrón"` |
| `--no-global-ignore-file` | Ignora el fichero de ignore global. | `fd --no-global-ignore-file "patrón"` |
| `--ignore-file=<fichero>` | Añade un fichero de ignore adicional. | `fd --ignore-file=.customignore "patrón"` |
| `-u` / `--unrestricted` | Equivale a `-HI` (muestra ocultos y no respeta ignore). Doble `-u` (`-uu`) también busca en binarios. | `fd -u "patrón"` |

---

## Filtrado por metadatos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--changed-within=<fecha\|duración>` | Solo ficheros modificados dentro del período indicado. Formatos: `10min`, `2h`, `1d`, `1week`, `2months`, o fecha ISO. | `fd --changed-within=1d` |
| `--changed-before=<fecha\|duración>` | Solo ficheros modificados antes del período indicado. | `fd --changed-before=1month "*.log"` |
| `-S <tamaño>` / `--size=<expresión>` | Filtra por tamaño. Prefijos: `+` mayor que, `-` menor que. Sufijos: `k`, `m`, `g`, `t`, `ki`, `mi`, `gi`. | `fd -S +10m` / `fd -S -1k` |
| `--owner=<usuario[:grupo]>` | Filtra por propietario y/o grupo (nombre o UID/GID). | `fd --owner=root "patrón"` |

---

## Ejecución de comandos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-x <cmd>` / `--exec=<cmd>` | Ejecuta el comando para cada resultado. Placeholder `{}` para la ruta, `{/}` nombre, `{.}` sin extensión, `{//}` directorio padre, `{/.}` nombre sin extensión. | `fd -e jpg -x convert {} {.}.png` |
| `-X <cmd>` / `--exec-batch=<cmd>` | Ejecuta el comando una sola vez con todos los resultados como argumentos. | `fd -e log -X rm` |
| `--batch-size=<N>` | Con `-X`, ejecuta el comando en lotes de N argumentos. | `fd -e log --batch-size=100 -X rm` |

---

## Salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` / `--list-details` | Formato largo con permisos, propietario, tamaño y fecha (como `ls -l`). | `fd -l "*.py"` |
| `--color=<auto\|always\|never>` | Control de colores. | `fd --color=never "patrón"` |
| `-c <color>` | Alias de `--color`. | `fd -c never` |
| `--absolute-path` / `-a` | Muestra rutas absolutas en lugar de relativas. | `fd -a "main.py"` |
| `--base-directory=<dir>` / `-b` | Usa `dir` como directorio base en lugar del actual. | `fd -b /etc "*.conf"` |
| `--relative-path` | Rutas relativas al directorio de búsqueda (por defecto). | — |
| `-0` / `--print0` | Separa resultados con NUL en lugar de newline. Compatible con `xargs -0`. | `fd -0 "*.log" \| xargs -0 rm` |
| `--max-results=<N>` | Limita el número de resultados. | `fd --max-results=10 "patrón"` |
| `-1` | Devuelve como máximo 1 resultado (útil en scripts). | `fd -1 "config.toml"` |
| `--show-errors` | Muestra errores de permisos y otros en stderr. | `fd --show-errors "patrón"` |
| `--strip-cwd-prefix` | Elimina el prefijo `./` de las rutas relativas. | `fd --strip-cwd-prefix "*.rs"` |

---

## Seguimiento de symlinks y sistema de ficheros

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-L` / `--follow` | Sigue enlaces simbólicos. | `fd -L "*.conf"` |
| `--one-file-system` / `-x` (contexto) | No cruza límites de sistemas de ficheros. | `fd --one-file-system "patrón" /` |

---

## Casos de uso comunes

```bash
# Buscar por nombre (regex)
fd "config"

# Solo ficheros .py
fd -e py

# Solo directorios que coincidan
fd -t d "test"

# Buscar incluyendo ocultos y sin ignorar
fd -HI "*.env"

# Ficheros modificados en las últimas 24h
fd --changed-within=24h

# Ficheros mayores de 100MB
fd -S +100m

# Ejecutar comando en cada resultado
fd -e log -x gzip {}

# Borrar todos los ficheros .tmp (con confirmación implícita)
fd -e tmp -X rm

# Buscar en directorio específico
fd "main" /home/usuario/proyectos/

# Ruta completa absoluta
fd -a "README"

# Integrar con fzf
fd -t f | fzf

# Ficheros ejecutables en PATH
fd -t x . /usr/bin

# NUL-separated para xargs
fd -0 -e log | xargs -0 wc -l

# Excluir ciertos tipos (usando -I + --ignore-file o negación en regex)
fd "patrón" -E "*.min.js"
```
