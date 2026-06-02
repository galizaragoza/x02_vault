sh es el intérprete de comandos estándar POSIX: lee comandos de un script, de stdin o de una cadena y los ejecuta. En la mayoría de distribuciones modernas `/bin/sh` es un enlace a una implementación ligera (`dash` en Debian/Ubuntu, `busybox sh` en sistemas embebidos) o a `bash`/`mksh` en modo POSIX. Escribir para `sh` garantiza portabilidad: evita extensiones (bashismos) y se limita a la especificación POSIX. Esta guía cubre las opciones definidas por POSIX, presentes en cualquier `sh` conforme; algunas implementaciones añaden las suyas.

```
sh [opciones] [-c cadena | fichero] [argumentos...]
```

---

## Modos de invocación

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-c <cadena>` | Lee y ejecuta los comandos desde la cadena indicada. | `sh -c 'echo $((2+2))'` |
| `-s` | Lee comandos de stdin (modo por defecto si no hay fichero ni `-c`). | `echo 'id' \| sh -s` |
| `-i` | Modo interactivo: muestra prompt y trata las señales de forma interactiva. | `sh -i` |
| `fichero` | Ejecuta el script indicado; los argumentos siguientes pasan a `$1`, `$2`... | `sh script.sh arg1 arg2` |

> Si el nombre de invocación empieza por `-` (p. ej. `-sh`), el shell actúa como **login shell** y lee los perfiles de inicio. Muchas implementaciones (dash, bash) aceptan además `-l`/`--login` para forzarlo.

---

## Opciones de comportamiento (`set`)

Estas opciones de una letra se pueden pasar en la invocación, activar en ejecución con `set -X` y desactivar con `set +X`.

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-e` | `errexit`: termina el shell si un comando devuelve estado distinto de cero. | `sh -e script.sh` |
| `-u` | `nounset`: error al expandir una variable no definida. | `set -u` |
| `-x` | `xtrace`: imprime cada comando expandido antes de ejecutarlo (precedido de `$PS4`). | `sh -x script.sh` |
| `-v` | `verbose`: imprime cada línea de entrada según se lee. | `sh -v script.sh` |
| `-n` | `noexec`: lee los comandos pero no los ejecuta (verificación de sintaxis). | `sh -n script.sh` |
| `-f` | `noglob`: deshabilita la expansión de comodines (globbing). | `set -f` |
| `-C` | `noclobber`: impide que `>` sobrescriba ficheros existentes (usar `>\|`). | `set -C` |
| `-a` | `allexport`: exporta automáticamente toda variable que se define o modifica. | `set -a` |
| `-b` | `notify`: notifica de inmediato la finalización de trabajos en segundo plano. | `set -b` |
| `-m` | `monitor`: habilita el control de trabajos (job control). | `set -m` |
| `-h` | `hashall`: localiza y recuerda la ruta de los comandos al definir funciones. | `set -h` |
| `-o <opción>` | Activa una opción larga con nombre (ver tabla siguiente). | `set -o errexit` |
| `+o <opción>` | Desactiva la opción larga indicada. | `set +o xtrace` |
| `-o` (sin arg.) | Lista el estado de todas las opciones. | `set -o` |

---

## Opciones largas para `-o` (POSIX)

| Nombre | Equivale a | Descripción |
|--------|-----------|-------------|
| `allexport` | `-a` | Exporta variables automáticamente. |
| `errexit` | `-e` | Sale ante error de un comando. |
| `ignoreeof` | — | No sale del shell interactivo con EOF (Ctrl-D). |
| `monitor` | `-m` | Control de trabajos. |
| `noclobber` | `-C` | Protege ficheros frente a `>`. |
| `noexec` | `-n` | No ejecuta (chequeo de sintaxis). |
| `noglob` | `-f` | Desactiva globbing. |
| `nolog` | — | No registra definiciones de funciones en el historial. |
| `notify` | `-b` | Notifica fin de trabajos en background. |
| `nounset` | `-u` | Error con variables no definidas. |
| `verbose` | `-v` | Eco de la entrada. |
| `vi` | — | Modo de edición de línea estilo vi. |
| `xtrace` | `-x` | Traza de ejecución. |

---

## Parámetros especiales relacionados con la invocación

| Parámetro | Descripción | Ejemplo |
|-----------|-------------|---------|
| `$0` | Nombre del script o del shell. | `echo $0` |
| `$1 … $9`, `${10}` | Argumentos posicionales pasados al script. | `echo "$1"` |
| `$#` | Número de argumentos posicionales. | `echo $#` |
| `$@` / `$*` | Lista de todos los argumentos (`"$@"` preserva el entrecomillado). | `for a in "$@"; do echo "$a"; done` |
| `$?` | Estado de salida del último comando. | `cmd; echo $?` |
| `$$` | PID del shell actual. | `echo $$` |
| `$!` | PID del último proceso en background. | `sleep 5 & echo $!` |
| `$-` | Opciones activas actualmente. | `echo $-` |

---

## Casos de uso comunes

```bash
# Verificar sintaxis de un script sin ejecutarlo
sh -n script.sh

# Depurar mostrando cada comando expandido
sh -x script.sh

# Ejecutar una orden puntual
sh -c 'for i in 1 2 3; do echo "$i"; done'

# Script robusto: abortar ante error o variable no definida
set -eu

# Combinar trazado y abort en cabecera de script portable
#!/bin/sh
set -eux

# Ejecutar comandos pasados por stdin
cat tareas.sh | sh -s

# Comprobar a qué implementación apunta /bin/sh
ls -l /bin/sh && readlink -f /bin/sh
```
