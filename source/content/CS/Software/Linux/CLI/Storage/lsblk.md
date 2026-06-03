lsblk (paquete `util-linux`) lista los dispositivos de bloque del sistema en forma de árbol, reflejando la jerarquía padre-hijo: discos, particiones, volúmenes LVM, RAID y multipath. Lee la información de `sysfs`, `udev` y `libblkid`, sin necesidad de root para la vista básica. No muestra dispositivos RAM por defecto. Es la herramienta estándar para inspeccionar topología de almacenamiento, puntos de montaje y filesystems de un vistazo.

```
lsblk [opciones] [dispositivo...]
```

---

## Selección de dispositivos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` / `--all` | Incluye dispositivos vacíos (size 0). | `lsblk -a` |
| `-A` / `--noempty` | Omite dispositivos vacíos. | `lsblk -A` |
| `-d` / `--nodeps` | No muestra hijos (particiones/holders); solo el dispositivo en sí. | `lsblk -d` |
| `-I list` / `--include` | Solo dispositivos con los major numbers indicados. | `lsblk -I 8` |
| `-e list` / `--exclude` | Excluye major numbers (por defecto excluye RAM disks, major 1). | `lsblk -e 7` |
| `-s` / `--inverse` | Invierte el árbol: muestra dependencias de abajo a arriba (holders→slaves). | `lsblk -s /dev/dm-0` |
| `-S` / `--scsi` | Solo dispositivos SCSI. | `lsblk -S` |
| `-N` / `--nvme` | Solo dispositivos NVMe. | `lsblk -N` |
| `-v` / `--virtio` | Solo dispositivos virtio. | `lsblk -v` |
| `-M` / `--merge` | Agrupa los padres de subárboles (RAID, multipath). | `lsblk -M` |

---

## Información mostrada

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-f` / `--fs` | Información de filesystems (FSTYPE, LABEL, UUID, uso, montaje). | `lsblk -f` |
| `-m` / `--perms` | Información de permisos (owner, group, mode). | `lsblk -m` |
| `-t` / `--topology` | Información de topología (sectores, alineación, cola, RA, rotacional). | `lsblk -t` |
| `-S` / `--scsi` | Datos SCSI (transporte, vendor, modelo, HCTL). | `lsblk -S` |
| `-z` / `--zoned` | Información de zonas (Zoned Block Devices). | `lsblk -z` |
| `-D` / `--discard` | Capacidades de descarte/TRIM. | `lsblk -D` |
| `-o list` / `--output` | Selecciona columnas a mostrar (ver tabla de columnas). | `lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINT` |
| `-O` / `--output-all` | Muestra todas las columnas disponibles. | `lsblk -O` |
| `-H` / `--list-columns` | Lista las columnas disponibles y sale. | `lsblk -H` |

---

## Formato de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` / `--list` | Salida en lista plana (sin árbol). | `lsblk -l` |
| `-T[=col]` / `--tree[=col]` | Fuerza salida en árbol (opcionalmente sobre una columna). | `lsblk -T` |
| `-J` / `--json` | Salida en JSON. | `lsblk -J` |
| `-P` / `--pairs` | Salida `CLAVE="valor"` (parseable por scripts). | `lsblk -P -o NAME,SIZE` |
| `-r` / `--raw` | Salida cruda sin alineación (separada por espacios). | `lsblk -r` |
| `-n` / `--noheadings` | No imprime la fila de cabeceras. | `lsblk -no NAME` |
| `-b` / `--bytes` | Tamaños en bytes en vez de formato legible. | `lsblk -b` |
| `-i` / `--ascii` | Usa solo caracteres ASCII para el árbol. | `lsblk -i` |
| `-p` / `--paths` | Muestra la ruta completa del dispositivo (`/dev/sda`). | `lsblk -p` |
| `-w num` / `--width` | Ancho de salida en caracteres. | `lsblk -w 120` |
| `-x col` / `--sort` | Ordena la salida por una columna. | `lsblk -x SIZE` |
| `-E col` / `--dedup` | Deduplica filas por columna. | `lsblk -E WWN` |
| `-y` / `--shell` | Nombres de columna válidos como variables de shell. | `lsblk -y -P` |

---

## Columnas frecuentes (`-o`)

Lista completa con `lsblk -H`. Las más usadas:

| Columna | Descripción |
|---------|-------------|
| `NAME` | Nombre del dispositivo (`sda`, `sda1`, `nvme0n1`). |
| `KNAME` | Nombre interno del kernel. |
| `PATH` | Ruta completa al nodo de dispositivo. |
| `MAJ:MIN` | Número major:minor del dispositivo. |
| `SIZE` | Tamaño del dispositivo. |
| `TYPE` | Tipo: `disk`, `part`, `lvm`, `crypt`, `raid1`, `rom`, `loop`. |
| `FSTYPE` | Tipo de filesystem (`ext4`, `xfs`, `vfat`, `LVM2_member`). |
| `FSVER` | Versión del filesystem. |
| `LABEL` / `PARTLABEL` | Etiqueta del filesystem / de la partición. |
| `UUID` / `PARTUUID` | UUID del filesystem / de la partición. |
| `MOUNTPOINT` | Punto de montaje (vacío si no montado). |
| `MOUNTPOINTS` | Todos los puntos de montaje del dispositivo. |
| `FSAVAIL` / `FSUSED` / `FSUSE%` | Espacio libre / usado / % de uso del filesystem. |
| `RM` | Dispositivo extraíble (`1`/`0`). |
| `RO` | Solo lectura (`1`/`0`). |
| `ROTA` | Rotacional (`1` HDD, `0` SSD/NVMe). |
| `TRAN` | Transporte (`sata`, `nvme`, `usb`, `scsi`). |
| `MODEL` / `VENDOR` / `SERIAL` | Identificadores del hardware. |
| `WWN` | Identificador único de almacenamiento. |
| `HCTL` | Host:Channel:Target:Lun (SCSI). |
| `PHY-SEC` / `LOG-SEC` | Tamaño de sector físico / lógico. |
| `STATE` | Estado del dispositivo (`running`, `suspended`). |

---

## Casos de uso comunes

```bash
# Vista clásica con filesystems y montajes
lsblk -f

# Solo discos físicos, sin particiones, con modelo y transporte
lsblk -d -o NAME,SIZE,ROTA,TRAN,MODEL

# Distinguir SSD (ROTA=0) de HDD (ROTA=1)
lsblk -d -o NAME,ROTA

# UUIDs para fstab
lsblk -o NAME,UUID,MOUNTPOINT

# Salida parseable por scripts
lsblk -P -o NAME,SIZE,TYPE,MOUNTPOINT

# Árbol inverso de un volumen LVM (qué discos lo respaldan)
lsblk -s /dev/mapper/vg01-datos
```
