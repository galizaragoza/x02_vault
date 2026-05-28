sort ordena líneas de texto de ficheros o stdin, por defecto en orden lexicográfico ascendente. Soporta ordenación numérica, por columnas, por versión, por mes y aleatoria. Puede eliminar duplicados, verificar si la entrada ya está ordenada y fusionar ficheros ya ordenados. Es fundamental en pipelines para preparar datos antes de `uniq`, `comm` o procesamiento posterior.

```
sort [opciones] [fichero...]
```

---

## Tipo de ordenación

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| (por defecto) | Orden lexicográfico byte a byte (ASCII). `10` va antes que `9`. | `sort nombres.txt` |
| `-n` / `--numeric-sort` | Orden numérico. Ignora blancos iniciales, trata `0x` como hex con `-n`. | `sort -n numeros.txt` |
| `-h` / `--human-numeric-sort` | Orden numérico para sufijos de tamaño: `1K < 1M < 1G`. Entiende salida de `du -h`. | `du -sh */ \| sort -h` |
| `-V` / `--version-sort` | Orden natural de versiones: `1.2` < `1.10`. | `sort -V versiones.txt` |
| `-M` / `--month-sort` | Ordena por nombre de mes abreviado en inglés (JAN < FEB < ... < DEC). | `sort -M meses.txt` |
| `-R` / `--random-sort` | Orden aleatorio (shuffle). Cada ejecución produce resultado diferente. | `sort -R wordlist.txt` |
| `-f` / `--ignore-case` | Insensible a mayúsculas/minúsculas. | `sort -f dominios.txt` |
| `-b` / `--ignore-leading-blanks` | Ignora blancos iniciales al comparar. | `sort -b data.txt` |

---

## Campos y delimitadores

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t <sep>` / `--field-separator=<sep>` | Define el separador de campos. Por defecto: cualquier secuencia de blancos. | `sort -t: -k3n /etc/passwd` |
| `-k <POS1>[,<POS2>]` / `--key=<POS>` | Ordena por el campo o rango de campos indicado. Formato: `campo[.carácter][opción]`. Las opciones se adjuntan directamente (ej. `3,3n`). | `sort -t, -k2,2n -k1,1 datos.csv` |

**Sintaxis de campo `-k`:** `CAMPO_INICIO[.CHAR_INICIO][OPTS][,CAMPO_FIN[.CHAR_FIN][OPTS]]`
- Campos numerados desde 1.
- `.CHAR`: posición del carácter dentro del campo (desde 1).
- Opciones por campo: `n` (numérico), `r` (inverso), `f` (case-insensitive), `b` (ignorar blancos), `M` (mes), `h` (human), `V` (versión).

---

## Modificadores de resultado

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r` / `--reverse` | Invierte el orden de la comparación. | `sort -rn numeros.txt` |
| `-u` / `--unique` | Elimina líneas duplicadas (solo conserva la primera de cada grupo igual). | `sort -u lista.txt` |
| `-s` / `--stable` | Ordenación estable: las líneas iguales mantienen su orden relativo original. | `sort -s -k1,1 datos.txt` |

---

## Verificación y fusión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-c` / `--check` | Verifica que la entrada ya está ordenada. Imprime diagnóstico y sale con código 1 si no lo está. | `sort -c fichero_ordenado.txt` |
| `-C` | Como `-c` pero sin diagnóstico (solo código de salida). | `sort -C fichero.txt && echo "OK"` |
| `-m` / `--merge` | Fusiona ficheros ya ordenados (más eficiente que ordenar de nuevo). | `sort -m fichero1_sorted.txt fichero2_sorted.txt` |

---

## Rendimiento y salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-o <fichero>` / `--output=<fichero>` | Escribe la salida en un fichero (puede ser el mismo de entrada). | `sort -o lista.txt lista.txt` |
| `-z` / `--zero-terminated` | Usa NUL como delimitador de línea (compatible con `find -print0`, `xargs -0`). | `find . -print0 \| sort -z` |
| `--parallel=<n>` | Número de hilos de ordenación paralela. Por defecto: número de CPUs. | `sort --parallel=4 fichero_grande.txt` |
| `-S <tamaño>` / `--buffer-size=<tamaño>` | Tamaño del buffer de memoria (ej. `512M`, `2G`). | `sort -S 1G fichero_grande.txt` |
| `-T <dir>` / `--temporary-directory=<dir>` | Directorio para ficheros temporales cuando no cabe en memoria. | `sort -T /tmp -S 500M enorme.txt` |

---

## Casos de uso comunes

```bash
# Ordenar alfabéticamente
sort nombres.txt

# Orden numérico
sort -n puertos.txt

# Orden inverso numérico
sort -rn tamanios.txt

# Eliminar duplicados
sort -u lista.txt

# Ordenar CSV por segunda columna numéricamente
sort -t, -k2,2n datos.csv

# Ordenar /etc/passwd por UID (campo 3)
sort -t: -k3,3n /etc/passwd

# Ordenar salida de du por tamaño
du -sh */ | sort -h

# Shuffle aleatorio de wordlist
sort -R /usr/share/wordlists/rockyou.txt > shuffled.txt

# Ordenar IPs (como texto de 4 partes numéricas)
sort -t. -k1,1n -k2,2n -k3,3n -k4,4n ips.txt

# Ordenar por versión
sort -V paquetes.txt

# Verificar que está ordenado
sort -c fichero.txt && echo "ordenado"
```
