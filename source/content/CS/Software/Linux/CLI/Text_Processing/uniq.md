`uniq` filtra líneas **adyacentes** repetidas de un fichero o de stdin, colapsando cada grupo de líneas iguales a su primera aparición. Solo detecta duplicados contiguos, por lo que la entrada suele ordenarse antes con `sort` (o usar directamente `sort -u`). Admite contar ocurrencias, mostrar solo únicas o solo repetidas, e ignorar campos, caracteres o mayúsculas al comparar. Forma parte de GNU coreutils y es habitual en pipelines de análisis de logs y wordlists.

```
uniq [OPCIÓN]... [ENTRADA [SALIDA]]
```

Si se omite `ENTRADA` (o es `-`) lee de stdin; si se omite `SALIDA` escribe en stdout. Un campo es una secuencia de blancos seguida de caracteres no blancos; **los campos se saltan antes que los caracteres**.

---

## Selección de líneas de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| (por defecto) | Colapsa cada grupo de líneas iguales adyacentes a su primera aparición. | `uniq lista.txt` |
| `-u` / `--unique` | Imprime **solo** las líneas que no se repiten (aparecen una única vez). | `uniq -u lista.txt` |
| `-d` / `--repeated` | Imprime **solo** las líneas duplicadas, una por grupo. | `uniq -d lista.txt` |
| `-D` | Imprime **todas** las líneas de cada grupo duplicado (no colapsa). | `uniq -D lista.txt` |
| `--all-repeated[=MÉTODO]` | Como `-D`, pero permite separar grupos con una línea vacía. `MÉTODO`: `none` (por defecto), `prepend`, `separate`. | `uniq --all-repeated=separate lista.txt` |

---

## Conteo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-c` / `--count` | Antepone a cada línea el número de ocurrencias del grupo. | `uniq -c lista.txt` |

---

## Agrupación

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--group[=MÉTODO]` | Muestra **todos** los ítems separando grupos con una línea vacía. `MÉTODO`: `separate` (por defecto), `prepend`, `append`, `both`. | `uniq --group=both lista.txt` |

---

## Criterio de comparación

Estas opciones controlan qué parte de cada línea se compara. Los campos (`-f`) se saltan antes que los caracteres (`-s`); `-w` limita cuántos caracteres se comparan a partir de ahí.

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` / `--ignore-case` | Ignora diferencias de mayúsculas/minúsculas al comparar. | `uniq -i dominios.txt` |
| `-f <N>` / `--skip-fields=<N>` | No compara los primeros `N` campos (útil para ignorar timestamps o índices al inicio). | `uniq -f1 log.txt` |
| `-s <N>` / `--skip-chars=<N>` | No compara los primeros `N` caracteres. | `uniq -s5 lista.txt` |
| `-w <N>` / `--check-chars=<N>` | Compara como mucho `N` caracteres de cada línea (tras aplicar `-f`/`-s`). | `uniq -w8 hashes.txt` |

---

## Formato de entrada/salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-z` / `--zero-terminated` | Usa `NUL` como delimitador de línea en lugar de salto de línea (compatible con `sort -z`, `find -print0`). | `sort -z f.txt \| uniq -z` |

---

## Información

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--help` | Muestra la ayuda y termina. | `uniq --help` |
| `--version` | Muestra la versión y termina. | `uniq --version` |

---

## Casos de uso comunes

```bash
# Colapsar duplicados (requiere entrada ordenada)
sort lista.txt | uniq

# Equivalente directo sin uniq
sort -u lista.txt

# Contar ocurrencias y ordenar de mayor a menor (top de IPs en un log)
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head

# Solo líneas que aparecen una única vez
sort lista.txt | uniq -u

# Solo líneas duplicadas (una por grupo)
sort lista.txt | uniq -d

# Todas las apariciones de las líneas duplicadas, grupos separados
sort lista.txt | uniq --all-repeated=separate

# Comparación insensible a mayúsculas
sort -f dominios.txt | uniq -i

# Deduplicar ignorando el primer campo (p. ej. fecha del log)
sort -k2 log.txt | uniq -f1

# Deduplicar por los primeros 8 caracteres (prefijo de hash)
sort hashes.txt | uniq -w8

# Contar líneas únicas de una wordlist grande sin cargarla dos veces
sort -u rockyou.txt | wc -l
```

---

## Notas

- `uniq` **no** detecta repeticiones no adyacentes: `a\nb\na` deja las dos `a`. Ordena primero con [[sort]].
- Para deduplicar sin ordenar (preservando el primer orden de aparición) usa `awk '!seen[$0]++'`, no `uniq`.
- `sort -u` colapsa duplicados en un solo paso, pero **no** cuenta (`-c`), no filtra solo-únicas (`-u` de uniq) ni solo-repetidas (`-d`); para eso hace falta `uniq`.
