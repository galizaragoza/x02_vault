**`xargs`** (arguments extended) es una utilidad de Unix/Linux que construye y ejecuta líneas de comando a partir de elementos leídos de la entrada estándar. Convierte una secuencia de argumentos de entrada en argumentos para otro comando, agrupándolos para respetar el límite de longitud de la línea de comandos del sistema (`ARG_MAX`) y evitar el error `Argument list too long`. Es la pieza estándar para encadenar la salida de `find`, `grep`, `ls`, etc. con comandos que no leen de stdin. Referencia: GNU findutils 4.11.

```
comando | xargs [opciones] comando_a_ejecutar [args_iniciales]
```

---

## Entrada: origen y delimitadores

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-0`, `--null` | Separa la entrada por NUL en vez de espacios/saltos (seguro con nombres raros; par de `find -print0`). | `find . -name "*.txt" -print0 \| xargs -0 rm` |
| `-a <archivo>`, `--arg-file=<archivo>` | Lee los argumentos desde un fichero en vez de stdin. | `xargs -a lista.txt echo` |
| `-d <delim>`, `--delimiter=<delim>` | Delimitador de entrada personalizado (desactiva el tratamiento de comillas). | `echo "a,b,c" \| xargs -d, -n1 echo` |
| `-E <eof-str>` | Cadena que marca fin lógico de entrada (ignora lo que venga después). | `echo "1 2 STOP 3" \| xargs -E STOP echo` |
| `-e[eof-str]`, `--eof[=eof-str]` | Variante GNU de `-E`; sin argumento desactiva la cadena EOF. | `xargs -e echo` |

---

## Control de invocación (cuántos argumentos por ejecución)

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-n <num>`, `--max-args=<num>` | Máximo de argumentos por invocación del comando. | `echo "1 2 3 4 5" \| xargs -n 2 echo` |
| `-L <num>` | Ejecuta el comando por cada `<num>` líneas no vacías de entrada. | `cat datos.txt \| xargs -L 2 echo` |
| `-l[num]`, `--max-lines[=num]` | Sinónimo obsoleto de `-L` (default 1); mantenido por compatibilidad. | `cat datos.txt \| xargs -l echo` |
| `-I <replace-str>` | Reemplaza `<replace-str>` por cada elemento; implica `-L 1` y desactiva el troceo. | `ls *.txt \| xargs -I {} mv {} {}.bak` |
| `-i[replace-str]`, `--replace[=str]` | Sinónimo obsoleto de `-I` (default `{}`). | `ls \| xargs -i cp {} /backup/` |
| `-s <num>`, `--max-chars=<num>` | Longitud máxima (en caracteres) de la línea de comandos generada. | `find . -name "*.txt" \| xargs -s 10000 echo` |

---

## Paralelismo

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-P <num>`, `--max-procs=<num>` | Ejecuta hasta `<num>` procesos en paralelo (`0` = sin límite). | `seq 10 \| xargs -P 4 -n1 sleep` |
| `--process-slot-var=<var>` | Exporta una variable con el índice de "slot" a cada proceso paralelo. | `seq 5 \| xargs -P2 --process-slot-var=SLOT sh -c 'echo $SLOT'` |

---

## Seguridad e interacción

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-r`, `--no-run-if-empty` | No ejecuta el comando si la entrada está vacía (evita ejecuciones espurias). | `find . -name "*.tmp" \| xargs -r rm` |
| `-p`, `--interactive` | Pide confirmación (`y/n`) antes de cada ejecución; implica `-t`. | `ls *.log \| xargs -p rm` |
| `-o`, `--open-tty` | Reabre stdin desde `/dev/tty` en el hijo (para comandos interactivos como `vi`). | `find . -name "*.conf" \| xargs -o vi` |
| `-x`, `--exit` | Aborta si una línea de comandos supera el límite de tamaño (`-s`/`-n`). | `find / -name "*" \| xargs -x ls -la` |

---

## Diagnóstico

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-t`, `--verbose` | Imprime en stderr cada comando antes de ejecutarlo. | `echo "f1 f2" \| xargs -t rm` |
| `--show-limits` | Muestra los límites del sistema para la longitud de argumentos. | `xargs --show-limits < /dev/null` |
| `--help` | Muestra la ayuda y sale. | `xargs --help` |
| `--version` | Muestra la versión y sale. | `xargs --version` |

---

## Casos prácticos

```bash
# Borrado seguro de resultados de find (NUL-safe + sin ejecución en vacío)
find . -name "*.tmp" -print0 | xargs -0 -r rm

# Renombrado con placeholder
ls *.txt | xargs -I {} mv {} {}.bak

# Descarga paralela de una lista de URLs (8 en vuelo)
xargs -a urls.txt -P 8 -n 1 curl -sO

# grep sobre miles de ficheros sin superar ARG_MAX
find . -type f -print0 | xargs -0 grep -l "password"

# Confirmación previa antes de un rm masivo
find . -name "core.*" | xargs -p rm
```

> **Pitfall**: por defecto `xargs` trocea por espacios *y* interpreta comillas, lo que rompe con nombres que contienen espacios o saltos. Con entrada de `find`, usar siempre `find -print0 | xargs -0`.

Ver también [[find]] · [[grep]] · [[fd-better-find]] · [[parallel]].
