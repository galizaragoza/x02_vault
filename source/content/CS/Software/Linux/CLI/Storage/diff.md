diff compara dos ficheros línea a línea y muestra las diferencias. Es la herramienta estándar para generar parches (formato `patch`), revisar cambios entre versiones de código y comparar directorios. El formato de salida por defecto usa `<` para líneas del primer fichero y `>` para el segundo; el formato unificado (`-u`) es el más usado en la práctica (es el que genera `git diff`).

```
diff [opciones] fichero1 fichero2
diff [opciones] directorio1 directorio2
diff [opciones] -U N fichero1 fichero2
```

---

## Formato de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| (por defecto) | Formato normal: muestra rangos de líneas con `<` y `>`. | `diff a.txt b.txt` |
| `-u` / `-U <N>` / `--unified[=N]` | Formato unificado con N líneas de contexto (por defecto: 3). Es el formato de `git diff` y `patch`. | `diff -u original.c modificado.c` |
| `-c` / `-C <N>` / `--context[=N]` | Formato de contexto antiguo con N líneas de contexto (por defecto: 3). | `diff -c a.txt b.txt` |
| `-y` / `--side-by-side` | Muestra ambos ficheros lado a lado con `|` marcando diferencias. | `diff -y a.txt b.txt` |
| `-W <n>` / `--width=<n>` | Ancho de columna en modo `-y`. | `diff -y -W 120 a.txt b.txt` |
| `--suppress-common-lines` | Con `-y`, oculta las líneas idénticas (solo muestra diferencias). | `diff -y --suppress-common-lines a.txt b.txt` |
| `--color[=CUANDO]` | Colorea la salida. `CUANDO`: `always`, `auto`, `never`. | `diff --color a.txt b.txt` |
| `-e` / `--ed` | Produce un script `ed` para transformar fichero1 en fichero2. | `diff -e original modificado` |

---

## Directorios

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r` / `--recursive` | Compara directorios recursivamente. | `diff -r dir1/ dir2/` |
| `-N` / `--new-file` | Trata los ficheros ausentes en uno de los lados como vacíos. | `diff -rN dir1/ dir2/` |
| `--no-dereference` | No dereferencia symlinks. | `diff --no-dereference dir1/ dir2/` |
| `-x <patrón>` / `--exclude=<patrón>` | Excluye ficheros que coincidan con el patrón. | `diff -r --exclude='*.o' dir1/ dir2/` |
| `-X <fichero>` / `--exclude-from=<fichero>` | Lee patrones de exclusión desde fichero. | `diff -rX .diffignore dir1/ dir2/` |

---

## Comparación de texto

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` / `--ignore-case` | Ignora diferencias de mayúsculas/minúsculas. | `diff -i a.txt b.txt` |
| `-w` / `--ignore-all-space` | Ignora todas las diferencias de espacios en blanco. | `diff -w a.txt b.txt` |
| `-b` / `--ignore-space-change` | Ignora cambios en cantidad de blancos (pero no blancos vs no-blancos). | `diff -b a.txt b.txt` |
| `-B` / `--ignore-blank-lines` | Ignora cambios que solo son líneas en blanco. | `diff -B a.txt b.txt` |
| `-E` / `--ignore-tab-expansion` | Ignora diferencias causadas por expansión de tabulaciones. | `diff -E a.txt b.txt` |
| `-Z` / `--ignore-trailing-space` | Ignora espacios al final de línea. | `diff -Z a.txt b.txt` |
| `-a` / `--text` | Trata todos los ficheros como texto aunque parezcan binarios. | `diff -a bin1 bin2` |

---

## Salida y diagnóstico

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-q` / `--brief` | Solo indica si los ficheros difieren, sin mostrar qué cambia. | `diff -q a.txt b.txt` |
| `-s` / `--report-identical-files` | Informa explícitamente cuando dos ficheros son idénticos. | `diff -s a.txt b.txt` |
| `-p` / `--show-c-function` | En código C, muestra el nombre de la función donde ocurre cada cambio. | `diff -up original.c modificado.c` |
| `-l` / `--paginate` | Envía la salida por `pr` para paginar. | `diff -l a.txt b.txt` |

---

## Casos de uso comunes

```bash
# Comparación básica
diff original.txt modificado.txt

# Formato unificado (para parches)
diff -u original.txt modificado.txt > cambios.patch

# Aplicar parche
patch original.txt < cambios.patch

# Comparar directorios
diff -rq dir1/ dir2/

# Comparar directorios con contexto
diff -ru dir1/ dir2/

# Solo saber si son iguales o no
diff -q fichero1 fichero2 && echo "idénticos"

# Ignorar espacios y mayúsculas
diff -iw a.txt b.txt

# Lado a lado, solo diferencias
diff -y --suppress-common-lines a.txt b.txt | head -20

# Comparar dos comandos
diff <(ls dir1/) <(ls dir2/)

# Diff de binarios como texto hex
diff <(xxd bin1) <(xxd bin2)
```
