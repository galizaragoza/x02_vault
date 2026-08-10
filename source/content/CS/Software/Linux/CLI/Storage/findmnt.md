`findmnt` lista y busca sistemas de archivos montados. Lee la información de tres fuentes posibles —la tabla del kernel (`/proc/self/mountinfo`), `/etc/fstab` y `/etc/mtab`— y la presenta por defecto en formato de árbol jerárquico. Forma parte del paquete `util-linux` y resulta más preciso que parsear `mount` o `/proc/mounts` a mano, porque expone columnas estructuradas, salida JSON/raw y filtrado por origen, destino, tipo u opciones. Es la herramienta de referencia para inspeccionar montajes, resolver etiquetas/UUID a dispositivos y monitorizar cambios de montaje en tiempo real.

```
findmnt [opciones]
findmnt [opciones] dispositivo|punto_de_montaje
findmnt [opciones] [--source] dispositivo [--target ruta | --mountpoint pto_montaje]
```

```sh
findmnt
## Output template (árbol)

TARGET     SOURCE    FSTYPE OPTIONS
/          /dev/sda2 ext4   rw,relatime
└─/boot    /dev/sda1 vfat   rw,relatime,fmask=0022
## <punto de montaje> <dispositivo> <tipo de fs> <opciones de montaje>
```

> Nota: la salida por defecto puede cambiar entre versiones. En scripts, define siempre las columnas con `-o`/`--output` para una salida estable.

---

## Fuente de datos (origen de la información)

Controlan **de dónde** lee `findmnt` la tabla de montajes. Por defecto usa el kernel (`mountinfo`).

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-k`, `--kernel[=método]` | Lee del kernel (por defecto). `método` puede ser `mountinfo` (lee `/proc/self/mountinfo`) o `listmount` (EXPERIMENTAL, vía syscalls `listmount(2)`/`statmount(2)`). | `findmnt --kernel=listmount` |
| `-m`, `--mtab` | Busca en `/etc/mtab`. Salida en formato lista; puede incluir opciones de montaje de espacio de usuario. | `findmnt -m` |
| `-s`, `--fstab` | Busca en `/etc/fstab`. Salida en formato lista. | `findmnt -s` |
| `-F`, `--tab-file ruta` | Busca en un fichero alternativo. Combinado con `--fstab`/`--mtab`/`--kernel` sobrescribe las rutas por defecto. Si se indica varias veces, deshabilita el árbol. | `findmnt -F /tmp/mountinfo.bak` |
| `-N`, `--task tid` | Usa el namespace `/proc/<tid>/mountinfo` en lugar del propio. Útil para inspeccionar montajes de otro proceso/namespace. | `findmnt -N 1234` |

---

## Selección y filtrado de sistemas de archivos

Restringen **qué** montajes se muestran. Las opciones `-t` y `-O` son acumulativas entre sí.

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-S`, `--source spec` | Define explícitamente el origen del montaje. Acepta `device`, `maj:min`, `LABEL=`, `UUID=`, `PARTLABEL=`, `PARTUUID=`. | `findmnt -S UUID=1234-5678` |
| `-T`, `--target ruta` | Define el destino (punto de montaje). Si `ruta` no es un mountpoint, recorre los elementos en orden inverso para hallarlo (solo en fuentes del kernel). | `findmnt -T /var/log` |
| `-M`, `--mountpoint ruta` | Define explícitamente el punto de montaje (fichero o directorio), sin recorrido inverso de elementos. | `findmnt -M /mnt/data` |
| `-t`, `--types lista` | Limita por tipo(s) de FS (lista separada por comas). El prefijo `no` excluye tipos. | `findmnt -t ext4,xfs` |
| `-O`, `--options lista` | Limita por opciones de montaje. Coincidencia exacta por ítem; `no` excluye un ítem y `+` desactiva la interpretación de `no`. | `findmnt -O ro,noexec` |
| `-Q`, `--filter expr` | Imprime solo los FS que cumplen una expresión (EXPERIMENTAL, sintaxis `scols-filter(5)`). | `findmnt -Q 'SIZE > 1073741824 && FSTYPE == "xfs"'` |
| `-i`, `--invert` | Invierte el sentido de la coincidencia. | `findmnt -i -t nfs` |
| `--id número` | Selecciona un FS por su mount node ID. | `findmnt --id 42` |
| `--uniq-id número` | Selecciona un FS por su mount node ID de 64 bits (usar con `--kernel=listmount`). | `findmnt --kernel=listmount --uniq-id 17179869185` |
| `-f`, `--first-only` | Imprime solo el primer FS coincidente. | `findmnt -f -t ext4` |
| `-A`, `--all` | Deshabilita todos los filtros internos e imprime todos los FS (incluidos pseudo). | `findmnt -A` |
| `--real` | Imprime solo sistemas de archivos reales. | `findmnt --real` |
| `--pseudo` | Imprime solo pseudo-sistemas de archivos (procfs, sysfs, cgroup, etc.). | `findmnt --pseudo` |
| `--shadowed` | Imprime solo FS que están tapados (over-mounted) por otro montaje. | `findmnt --shadowed` |
| `-U`, `--uniq` | Ignora FS con destinos de montaje duplicados (omite over-mounts). | `findmnt -U` |
| `-R`, `--submounts` | Imprime recursivamente todos los submontajes del FS seleccionado. Fuerza salida en árbol. Sin efecto en `--mtab`/`--fstab`. | `findmnt -R /` |
| `-d`, `--direction palabra` | Dirección de búsqueda: `forward` o `backward`. | `findmnt -d backward -T /home` |

---

## Formato y presentación de la salida

Controlan **cómo** se renderiza la tabla: columnas, tipo de salida, formateo de tamaños y rutas.

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-o`, `--output lista` | Define las columnas a imprimir. Con `+lista` extiende la lista por defecto. | `findmnt -o TARGET,SOURCE,FSTYPE,OPTIONS` |
| `--output-all` | Imprime casi todas las columnas disponibles (excluye las que requieren `--poll`). | `findmnt --output-all` |
| `-H`, `--list-columns` | Lista las columnas válidas para `--output`. Combinable con `--json`/`--raw`. | `findmnt -H` |
| `-l`, `--list` | Salida en formato lista (sin árbol). | `findmnt -l` |
| `--tree` | Fuerza salida en árbol cuando es posible; se ignora si la tabla no tiene relación padre-hijo (p.ej. fstab). | `findmnt --mtab --tree` |
| `-a`, `--ascii` | Usa caracteres ASCII para el árbol (en lugar de líneas Unicode). | `findmnt -a` |
| `-J`, `--json` | Salida en formato JSON. | `findmnt -J` |
| `-r`, `--raw` | Salida raw; caracteres inseguros se escapan en hex (`\x<código>`). | `findmnt -r` |
| `-P`, `--pairs` | Salida en pares `clave="valor"`; valores inseguros hex-escapados. | `findmnt -P` |
| `-y`, `--shell` | Modifica los nombres de columna para que sean identificadores de variable de shell válidos (útil con `--pairs`). | `findmnt -P -y` |
| `-n`, `--noheadings` | No imprime la línea de cabecera. | `findmnt -n` |
| `-u`, `--notruncate` | No trunca el texto en ninguna columna. | `findmnt -u` |
| `-b`, `--bytes` | Imprime tamaños en bytes en vez de en forma legible (K, M, G…). | `findmnt --df -b` |
| `-c`, `--canonicalize` | Canonicaliza todas las rutas impresas. | `findmnt -c` |
| `-C`, `--nocanonicalize` | No canonicaliza ninguna ruta (afecta comparación de rutas y evaluación de tags). | `findmnt -C -T /mnt/link` |
| `-v`, `--nofsroot` | No imprime `[/dir]` en la columna SOURCE para bind mounts o subvolúmenes btrfs. | `findmnt -v` |
| `-e`, `--evaluate` | Convierte tags (LABEL, UUID, PARTUUID, PARTLABEL) al nombre de dispositivo en SOURCE. Pensado para `--fstab`. | `findmnt -s -e` |
| `--vfs-all` | Con la columna `VFS-OPTIONS`, imprime también las flags VFS por defecto (auditoría). | `findmnt -o TARGET,VFS-OPTIONS --vfs-all` |
| `--hyperlink[=cuándo]` | Imprime rutas como hyperlinks de terminal. `cuándo` ∈ `always`/`never`/`auto` (por defecto `auto`). | `findmnt --hyperlink=always` |
| `-D`, `--df` | Imita la salida de `df(1)`: `-o SOURCE,FSTYPE,SIZE,USED,AVAIL,USE%,TARGET` excluyendo pseudo-FS. | `findmnt -D` |
| `-I`, `--dfi` | Imita `df -i` (inodos): `-o SOURCE,FSTYPE,INO.TOTAL,INO.USED,INO.AVAIL,INO.USE%,TARGET`. | `findmnt -I` |

---

## Monitorización en tiempo real (`--poll`)

`--poll` bloquea y reacciona a cambios en `/proc/self/mountinfo`. Habilita columnas extra propias del modo monitor.

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-p`, `--poll[=lista]` | Monitoriza cambios de montaje. Acciones: `mount`, `umount`, `remount`, `move` (todas por defecto). | `findmnt --poll=umount,move` |
| `-w`, `--timeout ms` | Límite superior de tiempo de bloqueo de `--poll`, en milisegundos. | `findmnt --poll -w 5000` |
| `-f`, `--first-only` | En modo poll, termina tras el primer evento (combinable con `--poll`). | `findmnt --poll=umount -f -M /mnt/foo` |

**Columnas extra disponibles solo bajo `--poll`:**

| **Columna** | **Significado** |
| --- | --- |
| `ACTION` | Nombre de la acción (`mount`/`umount`/`move`/`remount`). Habilitada por defecto en poll. |
| `OLD-TARGET` | Destino previo; disponible para `umount` y `move`. |
| `OLD-OPTIONS` | Opciones previas; disponible para `umount` y `remount`. |

---

## Verificación de la tabla de montajes

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-x`, `--verify` | Comprueba el contenido de la tabla (por defecto verifica que `/etc/fstab` es parseable y usable). Combinable con `--tab-file` y filtros source/target. | `findmnt --verify` |
| `--verbose` | Fuerza a `findmnt` a imprimir más información (por ahora solo con `--verify`). | `findmnt --verify --verbose` |

---

## Información y ayuda

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-h`, `--help` | Muestra la ayuda (incluye la lista de columnas soportadas) y sale. | `findmnt -h` |
| `-V`, `--version` | Muestra la versión y sale. | `findmnt -V` |

---

## Columnas SOURCE vs SOURCES

La relación entre dispositivos de bloque y FS no siempre es 1:1 (un FS puede usar varios dispositivos). Por eso:

- `SOURCE` — dispositivo único asociado al montaje.
- `SOURCES` (plural) — celda multilínea con **todos** los dispositivos donde se halla el mismo UUID del FS (u otro tag con `--fstab --evaluate`). En `--raw`/`--pairs` esta columna usa formato tipo array: `name=("aaa" "bbb" "ccc")`.

---

## Variables de entorno

| **Variable** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `LIBMOUNT_FSTAB=<ruta>` | Sobrescribe la ubicación por defecto de `fstab`. | `LIBMOUNT_FSTAB=/tmp/fstab findmnt -s` |
| `LIBMOUNT_MTAB=<ruta>` | Sobrescribe la ubicación por defecto de `mtab`. | `LIBMOUNT_MTAB=/tmp/mtab findmnt -m` |
| `LIBMOUNT_DEBUG=all` | Habilita salida de depuración de `libmount`. | `LIBMOUNT_DEBUG=all findmnt` |
| `LIBSMARTCOLS_DEBUG=all` | Habilita depuración de `libsmartcols`. | `LIBSMARTCOLS_DEBUG=all findmnt` |
| `LIBSMARTCOLS_DEBUG_PADDING=on` | Usa caracteres de padding visibles. | `LIBSMARTCOLS_DEBUG_PADDING=on findmnt` |
| `LIBSMARTCOLS_JSON=compact\|lines` | Controla el formato JSON de `--json`: `compact` (mínimo whitespace) o `lines` (JSON Lines, un objeto por línea). | `LIBSMARTCOLS_JSON=lines findmnt -J` |

---

## Códigos de salida

| **Código** | **Significado** |
| --- | --- |
| `0` | Hay algo que mostrar (coincidencia encontrada). |
| `1` | Error: ningún FS coincide con el filtro, o el dispositivo/punto de montaje no existe. |

---

## Ejemplos prácticos

```sh
findmnt --fstab -t nfs                                   # FS NFS definidos en /etc/fstab
findmnt --fstab /mnt/foo                                 # entradas fstab con mountpoint o source /mnt/foo
findmnt --fstab --target /mnt/foo                        # solo donde /mnt/foo es el mountpoint
findmnt --fstab --evaluate                               # resuelve LABEL=/UUID= a nombres de dispositivo
findmnt -n --raw --evaluate --output=target LABEL=/boot  # solo el mountpoint del FS con label /boot
findmnt --poll --mountpoint /mnt/foo                     # monitoriza mount/umount/remount/move en /mnt/foo
findmnt --poll=umount --first-only --mountpoint /mnt/foo # espera al umount de /mnt/foo
findmnt --poll=remount -t ext3 -O ro                     # monitoriza remontajes a read-only en ext3
```

---

Relacionado: [[(u)mount]] · [[lsblk]] · [[blkid]] · [[NFS]] · [[df]] · [[LVM]]
