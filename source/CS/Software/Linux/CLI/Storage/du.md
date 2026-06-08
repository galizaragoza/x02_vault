du (disk usage) estima el espacio ocupado por archivos y directorios, recorriendo recursivamente el árbol indicado. Reporta el uso real en disco (bloques asignados), no el tamaño lógico, salvo con `--apparent-size`. Es la herramienta estándar para localizar qué directorios consumen el almacenamiento.

```
du [OPCIONES] [ARCHIVO|DIRECTORIO]
```

---

## Selección y profundidad

| **Parámetro**                 | **Función**                                                   | **Ejemplo de Sintaxis**                 |
| ------------------------- | --------------------------------------------------------- | ----------------------------------- |
| `-a`, `--all`             | Muestra tamaño de todos los archivos, no solo directorios | `du -ah /tmp`                       |
| `-s`, `--summarize`       | Solo muestra el total para cada argumento (implica `-d 0`) | `du -sh /var/log`                   |
| `-d N`, `--max-depth=N`   | Limita la profundidad de directorios a mostrar (acumula los más profundos) | `du -h -d 1 /`             |
| `-S`, `--separate-dirs`   | No incluye tamaño de subdirectorios en directorios padres | `du -Sh /var`                       |
| `-t SIZE`, `--threshold=SIZE` | Solo muestra entradas con tamaño `>= SIZE` (negativo: `<=`) | `du -ah -t 100M /`              |
| `--exclude=PATTERN`       | Excluye archivos que coincidan con el patrón (glob)       | `du -h --exclude="*.log" /var`      |
| `-X FILE`, `--exclude-from=FILE` | Excluye patrones leídos de un fichero (uno por línea) | `du -h -X excl.txt /var`        |
| `--files0-from=F`         | Lee la lista de nombres (terminados en NUL) de un fichero (`-` = stdin) | `find . -type f -print0 \| du --files0-from=-` |

---

## Travesía del árbol y enlaces

| **Parámetro**                 | **Función**                                                   | **Ejemplo de Sintaxis**                 |
| ------------------------- | --------------------------------------------------------- | ----------------------------------- |
| `-x`, `--one-file-system` | No cruza límites de sistemas de archivos                  | `du -xh /`                          |
| `-L`, `--dereference`     | Sigue todos los enlaces simbólicos (calcula el destino)   | `du -Lh /usr/bin`                   |
| `-D`, `-H`, `--dereference-args` | Solo sigue los enlaces simbólicos pasados como argumento | `du -Dh /symlink`             |
| `-P`, `--no-dereference`  | No sigue enlaces simbólicos (comportamiento por defecto)  | `du -Ph /var`                       |
| `-l`, `--count-links`     | Cuenta los tamaños varias veces aunque haya hard links    | `du -lh /backup`                    |
| `-0`, `--null`            | Termina cada línea de salida con NUL en vez de salto de línea | `du -0 /var \| xargs -0`         |

---

## Formato de tamaño y salida

| **Parámetro**                 | **Función**                                                   | **Ejemplo de Sintaxis**                 |
| ------------------------- | --------------------------------------------------------- | ----------------------------------- |
| `-h`, `--human-readable`  | Tamaños legibles en potencias de 1024 (K, M, G)           | `du -h archivo.txt`                 |
| `--si`                    | Tamaños legibles en potencias de 1000 (kB, MB, GB)        | `du --si /var`                      |
| `-b`, `--bytes`           | Tamaño aparente en bytes (equivale a `--apparent-size -B 1`) | `du -b archivo.txt`              |
| `-k`                      | Tamaños en bloques de 1024 bytes (por defecto)            | `du -k /var`                        |
| `-m`                      | Tamaños en bloques de 1 MiB                               | `du -m /var`                        |
| `-B SIZE`, `--block-size=SIZE` | Escala los tamaños a bloques de `SIZE` (ej. `1M`, `512`) | `du -B 1M /var`                |
| `--apparent-size`         | Muestra tamaños aparentes (lógicos), no uso en disco real | `du -h --apparent-size archivo.txt` |
| `-c`, `--total`           | Muestra un total general al final                         | `du -ch *.log`                      |
| `--inodes`                | Cuenta uso de inodos en vez de bloques                    | `du --inodes -s /var`               |
| `--time`                  | Muestra fecha de última modificación                      | `du -h --time /var/log`             |
| `--time=WORD`             | Usa `atime`/`access`, `ctime`/`status` como marca de tiempo | `du --time=atime /var`            |
| `--time-style=STYLE`      | Formato de fecha (`full-iso`, `long-iso`, `iso`, `+FMT`)  | `du --time --time-style=iso /var`   |

---

## Casos de uso comunes

```bash
# Top 10 directorios más grandes bajo el directorio actual
du -h -d 1 | sort -rh | head

# Tamaño total de un directorio
du -sh /var/log

# Localizar archivos > 100 MB
du -ah / --exclude=/proc -t 100M 2>/dev/null

# Uso de inodos por subdirectorio (diagnóstico "No space left" con disco libre)
du --inodes -d 1 /var | sort -rn
```