mkfs crea (formatea) un sistema de archivos sobre un dispositivo de bloque o un archivo. En realidad es un front-end: invoca el binario específico `mkfs.<tipo>` (ej. `mkfs.ext4`, `mkfs.xfs`) según el tipo solicitado. Las opciones específicas de cada filesystem las interpreta ese binario, no el front-end. **Operación destructiva**: borra cualquier dato previo en el dispositivo; verificar siempre el destino antes de ejecutar.

```
mkfs [options] [-t type] [fs-options] device [size]
```

> Es más habitual y recomendable llamar directamente al binario por tipo (`mkfs.ext4 /dev/sdb1`) que usar el front-end `mkfs -t`.

---

## Front-end `mkfs`

| **Parámetro (Flag)** | **Función**                                    | **Ejemplo de Sintaxis**         |
| ---------------- | ------------------------------------------ | --------------------------- |
| `-t type`        | Especifica tipo de filesystem (invoca `mkfs.<type>`). | `mkfs -t ext4 /dev/sdb1`    |
| `-V`             | Modo verbose; muestra comandos ejecutados. | `mkfs -V -t ext4 /dev/sdb1` |
| `-h`             | Muestra ayuda.                             | `mkfs -h`                   |

---

## ext2/ext3/ext4 (`mkfs.ext4`)

| **Parámetro (Flag)** | **Función**                            | **Ejemplo de Sintaxis**                     |
| ---------------- | -------------------------------------- | ------------------------------------------- |
| `-L label`       | Establece etiqueta del volumen (máx. 16 chars). | `mkfs.ext4 -L mydata /dev/sdb1`     |
| `-b size`        | Tamaño de bloque en bytes (1024/2048/4096). | `mkfs.ext4 -b 4096 /dev/sdb1`          |
| `-m percent`     | Porcentaje de bloques reservados para root (def. 5%). | `mkfs.ext4 -m 1 /dev/sdb1`        |
| `-N number`      | Nº de inodos a crear explícitamente.   | `mkfs.ext4 -N 1000000 /dev/sdb1`            |
| `-i bytes`       | Ratio bytes/inodo (mayor = menos inodos). | `mkfs.ext4 -i 16384 /dev/sdb1`           |
| `-O features`    | Habilita/inhabilita características (prefijo `^` desactiva). | `mkfs.ext4 -O extents,dir_index /dev/sdb1` |
| `-E opts`        | Opciones extendidas (`lazy_itable_init`, `stride`, `discard`). | `mkfs.ext4 -E lazy_itable_init=1 /dev/sdb1` |
| `-j`             | Crea journal (implícito en ext4).      | `mkfs.ext4 -j /dev/sdb1`                    |
| `-c`             | Comprueba bloques defectuosos antes de crear (`-cc` = test r/w). | `mkfs.ext4 -c /dev/sdb1`        |
| `-F`             | Fuerza la creación aunque el dispositivo esté montado o no sea de bloque. | `mkfs.ext4 -F disk.img`  |
| `-n`             | Simulacro: no crea nada, muestra lo que haría. | `mkfs.ext4 -n /dev/sdb1`             |
| `-q`             | Modo silencioso.                       | `mkfs.ext4 -q /dev/sdb1`                    |
| `-v`             | Modo verbose.                          | `mkfs.ext4 -v /dev/sdb1`                    |
| `-U uuid`        | Asigna UUID específico (o `random`/`null`). | `mkfs.ext4 -U random /dev/sdb1`        |

---

## XFS (`mkfs.xfs`)

| **Parámetro (Flag)** | **Función**                            | **Ejemplo de Sintaxis**                     |
| ---------------- | -------------------------------------- | ------------------------------------------- |
| `-L label`       | Etiqueta del volumen (máx. 12 chars).  | `mkfs.xfs -L data /dev/sdb1`                |
| `-f`             | Fuerza la creación sobre un FS existente. | `mkfs.xfs -f /dev/sdb1`                  |
| `-b size=N`      | Tamaño de bloque (ej. `size=4096`).    | `mkfs.xfs -b size=4096 /dev/sdb1`           |
| `-d opts`        | Opciones de sección de datos (`agcount`, `su`, `sw`). | `mkfs.xfs -d agcount=8 /dev/sdb1` |
| `-l opts`        | Opciones del log/journal (`size`, `internal`, `logdev`). | `mkfs.xfs -l size=128m /dev/sdb1` |
| `-i opts`        | Opciones de inodos (`size`, `maxpct`). | `mkfs.xfs -i size=512 /dev/sdb1`            |
| `-m opts`        | Metadatos (`crc=1`, `reflink=1`, `uuid`). | `mkfs.xfs -m reflink=1 /dev/sdb1`        |
| `-N`             | Simulacro: muestra parámetros sin escribir. | `mkfs.xfs -N /dev/sdb1`                 |
| `-K`             | No descarta (TRIM) el dispositivo al formatear. | `mkfs.xfs -K /dev/sdb1`             |

---

## Btrfs (`mkfs.btrfs`)

| **Parámetro (Flag)** | **Función**                            | **Ejemplo de Sintaxis**                     |
| ---------------- | -------------------------------------- | ------------------------------------------- |
| `-L label`       | Etiqueta del volumen.                  | `mkfs.btrfs -L data /dev/sdb1`              |
| `-d profile`     | Perfil RAID de datos (`single`, `raid0`, `raid1`, `raid10`). | `mkfs.btrfs -d raid1 /dev/sdb /dev/sdc` |
| `-m profile`     | Perfil RAID de metadatos (`dup`, `single`, `raid1`...). | `mkfs.btrfs -m dup /dev/sdb1`  |
| `-f`             | Fuerza sobreescritura de FS existente. | `mkfs.btrfs -f /dev/sdb1`                   |
| `-n size`        | Tamaño de nodo del árbol B (def. 16K). | `mkfs.btrfs -n 16384 /dev/sdb1`             |
| `-s size`        | Tamaño de sector.                      | `mkfs.btrfs -s 4096 /dev/sdb1`              |
| `-O features`    | Habilita características en creación.   | `mkfs.btrfs -O no-holes /dev/sdb1`          |
| `-U uuid`        | Asigna UUID específico.                | `mkfs.btrfs -U <uuid> /dev/sdb1`            |

---

## FAT/vfat (`mkfs.vfat`)

| **Parámetro (Flag)** | **Función**                                    | **Ejemplo de Sintaxis**             |
| ---------------- | ------------------------------------------ | ------------------------------- |
| `-n label`       | Establece etiqueta del volumen (11 chars). | `mkfs.vfat -n USBDRV /dev/sdb1` |
| `-F size`        | Tamaño de FAT: 12, 16 o 32 bits.           | `mkfs.vfat -F 32 /dev/sdb1`     |
| `-s sectors`     | Sectores por cluster (potencia de 2).      | `mkfs.vfat -s 8 /dev/sdb1`      |
| `-S size`        | Tamaño de sector lógico en bytes.          | `mkfs.vfat -S 512 /dev/sdb1`    |
| `-I`             | Ignora advertencia al formatear el disco completo (no partición). | `mkfs.vfat -I /dev/sdb` |
| `-R count`       | Nº de sectores reservados.                 | `mkfs.vfat -R 32 /dev/sdb1`     |
| `-v`             | Modo verbose.                              | `mkfs.vfat -v /dev/sdb1`        |

---

## exFAT (`mkfs.exfat`)

| **Parámetro (Flag)** | **Función**                            | **Ejemplo de Sintaxis**                     |
| ---------------- | -------------------------------------- | ------------------------------------------- |
| `-L label`       | Etiqueta del volumen.                  | `mkfs.exfat -L data /dev/sdb1`              |
| `-c size`        | Tamaño de cluster (ej. `1M`, `128K`).  | `mkfs.exfat -c 1M /dev/sdb1`                |
| `-b size`        | Tamaño de sector/boundary alignment.   | `mkfs.exfat -b 4K /dev/sdb1`                |

---

## NTFS (`mkfs.ntfs` / `mkntfs`)

| **Parámetro (Flag)** | **Función**                            | **Ejemplo de Sintaxis**                     |
| ---------------- | -------------------------------------- | ------------------------------------------- |
| `-L label`       | Etiqueta del volumen.                  | `mkfs.ntfs -L data /dev/sdb1`               |
| `-Q`, `--quick`  | Formateo rápido (no comprueba bloques ni pone a cero). | `mkfs.ntfs -Q /dev/sdb1`    |
| `-f`, `--fast`   | Formateo rápido (alias de `-Q`).       | `mkfs.ntfs -f /dev/sdb1`                    |
| `-c size`        | Tamaño de cluster en bytes.            | `mkfs.ntfs -c 4096 /dev/sdb1`               |
| `-F`, `--force`  | Fuerza la creación aunque esté montado. | `mkfs.ntfs -F /dev/sdb1`                   |

---

## Casos de uso comunes

```bash
# ext4 con etiqueta, init rápido
mkfs.ext4 -L datos /dev/sdb1

# XFS forzado con reflink (snapshots/CoW)
mkfs.xfs -f -m reflink=1 /dev/sdb1

# USB FAT32
mkfs.vfat -F 32 -n USB /dev/sdb1

# Btrfs en espejo sobre dos discos
mkfs.btrfs -L pool -d raid1 -m raid1 /dev/sdb /dev/sdc
```