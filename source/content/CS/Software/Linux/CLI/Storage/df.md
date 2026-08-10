`df` (*disk free*) informa del espacio de disco usado y disponible en los sistemas de archivos montados. Por cada `FILE` indicado muestra el sistema de archivos que lo contiene; sin argumentos lista todos los sistemas de archivos montados. Los valores se expresan en bloques (por defecto 1 KiB) o en inodos, y admite filtrado por tipo y formatos de salida personalizables. Forma parte de GNU coreutils.

```
df [OPCIÓN]... [FICHERO]...
```

---

## Formato y unidades de tamaño

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-h` / `--human-readable` | Tamaños legibles en potencias de 1024 (`K`, `M`, `G`...; ej. `1023M`). | `df -h` |
| `-H` / `--si` | Tamaños legibles en potencias de 1000 (unidades SI; ej. `1.1G`). | `df -H` |
| `-k` | Equivale a `--block-size=1K` (bloques de 1 KiB). Es el formato POSIX por defecto. | `df -k` |
| `-B <SIZE>` / `--block-size=<SIZE>` | Escala los tamaños por `SIZE` antes de imprimir. Ej. `-BM` muestra en unidades de 1.048.576 bytes. | `df -BM /` |

**Formato de `SIZE`:** entero con unidad opcional. Potencias de 1024: `K, M, G, T, P, E, Z, Y, R, Q`. Potencias de 1000: `KB, MB, GB...`. También prefijos binarios explícitos: `KiB=K`, `MiB=M`, etc. Ejemplo: `10K` = 10×1024 bytes.

Las unidades de visualización siguen la primera fuente disponible entre `--block-size` y las variables de entorno `DF_BLOCK_SIZE`, `BLOCK_SIZE` y `BLOCKSIZE`. En su defecto son 1024 bytes (o 512 si `POSIXLY_CORRECT` está definido).

---

## Selección y filtrado de sistemas de archivos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` / `--all` | Incluye sistemas de archivos ficticios, duplicados e inaccesibles (ej. `proc`, `sysfs`, montajes con 0 bloques). | `df -a` |
| `-l` / `--local` | Limita el listado a los sistemas de archivos locales (excluye montajes de red como NFS/CIFS). | `df -l` |
| `-t <TYPE>` / `--type=<TYPE>` | Solo sistemas de archivos del tipo indicado. Repetible. | `df -t ext4` |
| `-x <TYPE>` / `--exclude-type=<TYPE>` | Excluye los sistemas de archivos del tipo indicado. Repetible. | `df -x tmpfs -x devtmpfs` |

---

## Información mostrada y columnas

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` / `--inodes` | Muestra información de inodos (total/usados/disponibles) en lugar de bloques. | `df -i` |
| `-T` / `--print-type` | Añade una columna con el tipo de sistema de archivos. | `df -T` |
| `-P` / `--portability` | Formato de salida POSIX: una línea por sistema de archivos, evita el truncado de dispositivos con nombre largo. | `df -P` |
| `--total` | Elide las entradas irrelevantes para el espacio disponible y añade una línea `total` con el gran total. | `df -h --total` |
| `--output[=FIELD_LIST]` | Formato definido por `FIELD_LIST` (columnas separadas por comas). Sin lista imprime todos los campos. Incompatible con `-i`/`-T` explícitos. | `df --output=source,fstype,pcent,target` |

**Campos válidos para `FIELD_LIST`:** `source` (dispositivo), `fstype` (tipo), `itotal`, `iused`, `iavail`, `ipcent` (inodos), `size`, `used`, `avail`, `pcent` (bloques), `file` (el `FICHERO` pasado en la línea de comandos) y `target` (punto de montaje).

---

## Sincronización

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--sync` | Invoca `sync` antes de leer el uso, para obtener cifras exactas (más lento). | `df --sync` |
| `--no-sync` | No invoca `sync` antes de leer el uso. Es el comportamiento por defecto. | `df --no-sync` |

---

## Información y misceláneos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v` | Ignorado. Aceptado por compatibilidad con otras versiones de `df`. | `df -v` |
| `--help` | Muestra la ayuda y termina. | `df --help` |
| `--version` | Muestra la versión y termina. | `df --version` |

---

## Casos de uso comunes

```bash
# Espacio libre legible de todos los montajes
df -h

# Legible + tipo de sistema de archivos
df -hT

# Solo particiones ext4
df -h -t ext4

# Excluir sistemas de archivos temporales/pseudo
df -h -x tmpfs -x devtmpfs -x squashfs

# Uso de inodos (útil cuando "no queda espacio" pero hay bloques libres)
df -ih

# Columnas concretas
df --output=source,fstype,size,used,avail,pcent,target -h

# Espacio del sistema de archivos que contiene un fichero/directorio
df -h /var/log/syslog

# Solo sistemas de archivos locales, con total
df -hl --total

# Detectar particiones al 100% de uso
df -h | awk 'NR==1 || $5=="100%"'
```
