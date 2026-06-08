bat es un reemplazo moderno de `cat` con resaltado de sintaxis, integración con Git para mostrar cambios, paginación automática vía `less`, números de línea y visualización de caracteres no imprimibles. Entiende más de 150 lenguajes de programación y formatos de fichero. Puede actuar como paginador para otros comandos (`man`, `git diff`, etc.) y usa la misma librería de sintaxis que el editor Sublime Text. Se instala como `bat` (o `batcat` en Debian/Ubuntu).

```
bat [opciones] [fichero...]
bat <subcomando> [opciones]
```

---

## Visualización de ficheros

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| (sin opciones) | Muestra el fichero con resaltado de sintaxis, numeración de líneas y delimitadores de sección. | `bat fichero.py` |
| `-n` / `--number` | Muestra solo los números de línea, sin los demás decoradores. | `bat -n fichero.rs` |
| `-A` / `--show-all` | Muestra caracteres no imprimibles (tabs, espacios, retornos de carro, etc.). | `bat -A fichero.txt` |
| `--show-nonprintable` | Equivale a `-A`. | `bat --show-nonprintable fichero` |
| `-p` / `--plain` | Sin números de línea, sin decoradores, sin paginación. Equivale a `cat`. | `bat -p fichero.txt` |
| `--paging=<never\|auto\|always>` | Controla la paginación. `never`: sin less. `auto`: pagina si la salida supera la pantalla. `always`: siempre pagina. Por defecto: `auto`. | `bat --paging=never fichero` |
| `--pager=<cmd>` | Comando paginador a usar. Por defecto: `less -FR`. | `bat --pager="less -RFX" fichero` |
| `-r <N:M>` / `--line-range=<N:M>` | Muestra solo las líneas del rango indicado. Formatos: `N:M`, `N:`, `:M`, `N`. | `bat -r 10:30 fichero.py` |
| `--highlight-line=<N>` / `-H <N>` | Resalta la línea N con fondo destacado. Acepta rangos `N:M`. | `bat -H 42 fichero.c` |

---

## Resaltado de sintaxis

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l <lenguaje>` / `--language=<lenguaje>` | Fuerza el lenguaje de resaltado en lugar de detectarlo automáticamente. | `bat -l json datos.txt` |
| `--list-languages` | Lista todos los lenguajes de sintaxis soportados con sus extensiones. | `bat --list-languages` |
| `--map-syntax=<patrón:sintaxis>` | Mapea un patrón glob a un lenguaje específico (persiste si se añade en config). | `bat --map-syntax='*.conf:INI'` |

---

## Temas y colores

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--theme=<tema>` | Tema de colores para el resaltado de sintaxis. | `bat --theme="Dracula" fichero.py` |
| `--list-themes` | Lista todos los temas disponibles con previsualización. | `bat --list-themes` |
| `--color=<auto\|always\|never>` | Controla el uso de colores ANSI. Por defecto: `auto`. | `bat --color=never fichero.py` |
| `--italic-text=<always\|never>` | Controla el uso de texto en cursiva (si el terminal lo soporta). Por defecto: `never`. | `bat --italic-text=always fichero.py` |

---

## Integración con Git

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--diff` / `-d` | Solo muestra líneas modificadas (añadidas/eliminadas) según Git, con contexto. Equivale a un `diff` visual. | `bat --diff fichero.py` |
| `--diff-context=<N>` | Número de líneas de contexto alrededor de los cambios con `--diff`. Por defecto: 2. | `bat --diff --diff-context=5 fichero.py` |
| (decoradores) | La columna izquierda muestra por defecto los cambios Git: `+` añadido, `-` eliminado, `~` modificado. Se controla con `--decorations`. | — |

---

## Decoradores y estilo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--decorations=<auto\|always\|never>` | Controla los decoradores (números de línea, marcas Git, bordes). | `bat --decorations=never fichero` |
| `--style=<lista>` | Selecciona qué decoradores mostrar. Lista separada por comas: `full`, `auto`, `plain`, `numbers`, `changes`, `header`, `header-filename`, `header-filesize`, `grid`, `rule`, `snip`. | `bat --style=numbers,changes fichero.py` |
| `--terminal-width=<N>` | Ancho del terminal a usar para calcular el layout. | `bat --terminal-width=100 fichero` |
| `--wrap=<auto\|never\|character>` | Controla el ajuste de línea. | `bat --wrap=never fichero.py` |
| `--tabs=<N>` | Define el ancho de tabulación en N espacios. Por defecto: 4. | `bat --tabs=2 fichero.py` |

---

## Ficheros y entrada

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-` | Lee de stdin. | `curl -s http://api/data \| bat -l json` |
| `--file-name=<nombre>` | Nombre a mostrar en la cabecera cuando se lee de stdin. | `cat fichero \| bat --file-name="output.py"` |
| `--ignored-suffix=<sufijo>` | Ignora el sufijo al detectar el lenguaje (ej. `.bak`). | `bat --ignored-suffix=.bak fichero.py.bak` |

---

## Caché y configuración

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--config-file` | Imprime la ruta del fichero de configuración. | `bat --config-file` |
| `--config-dir` | Imprime el directorio de configuración. | `bat --config-dir` |
| `--generate-config-file` | Genera un fichero de configuración con los valores por defecto. | `bat --generate-config-file` |

### Subcomando `bat cache`

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `bat cache --build` | Reconstruye la caché de sintaxis y temas tras añadir nuevos. | `bat cache --build` |
| `bat cache --clear` | Elimina la caché (vuelve a los temas/sintaxis por defecto). | `bat cache --clear` |

---

## Casos de uso comunes

```bash
# Ver fichero con resaltado
bat fichero.py

# Sin paginación ni decoradores (como cat)
bat -p fichero.txt

# Mostrar caracteres especiales (tabs, CRLF)
bat -A fichero.txt

# Solo líneas 50-100
bat -r 50:100 fichero.log

# Desde stdin con lenguaje forzado
curl -s https://api.github.com/repos/usuario/repo | bat -l json

# Resaltar línea de error específica
bat -H 42 script.py

# Ver solo cambios Git
bat --diff fichero.py

# Como paginador de man
man ls | bat -l man --paging=always

# Tema específico
bat --theme="Monokai Extended" fichero.js

# Comparar temas disponibles
bat --list-themes | head -20

# Sin colores (para pipes)
bat --color=never --plain fichero.py

# Ver varios ficheros
bat src/*.py

# Con estilo mínimo
bat --style=numbers fichero.c
```
