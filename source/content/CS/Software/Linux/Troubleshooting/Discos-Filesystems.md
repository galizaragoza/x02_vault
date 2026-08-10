# Diagnóstico de discos y filesystems

#Linux #Monitoring #Troubleshooting #Storage

Guía de refresco rápido para evaluar el estado del almacenamiento en Linux: espacio libre, **inodos**, qué ocupa el disco, rendimiento y latencia de E/S, salud física (SMART) y errores del sistema de ficheros. El objetivo es separar tres problemas distintos que se confunden: *disco lleno por bytes*, *disco lleno por inodos* y *disco lento por E/S*.

> Concepto base: `df` puede decir que **queda espacio** y aun así fallar la escritura porque **se agotaron los inodos** (`df -i`), o porque un fichero borrado sigue **abierto** por un proceso y no libera bloques (`lsof +L1`).

---

## 1. Espacio: bytes vs inodos

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `df -h` | Espacio por filesystem montado (legible). | `df -h` |
| `df -i` | **Inodos** usados/libres por filesystem. | `df -i` |
| `df -hT` | Añade el tipo de FS (ext4, xfs, tmpfs...). | `df -hT` |
| `df -h --output=source,fstype,size,used,avail,pcent,target` | Columnas a medida. | — |
| `df -h /ruta` | Solo el FS que contiene esa ruta. | `df -h /var/log` |

> XFS **no** preasigna inodos como ext4: `df -i` puede mostrar valores que crecen dinámicamente. En ext4 los inodos son fijos desde el formateo (`mkfs.ext4 -N`).

Ver [[df]].

---

## 2. ¿Qué ocupa el espacio?

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `du -sh *` | Tamaño por entrada del directorio actual. | `du -sh * \| sort -rh \| head` |
| `du -h --max-depth=1 /var` | Tamaño un nivel hacia abajo. | `du -h -d1 /var \| sort -rh` |
| `du -xh / --max-depth=1` | `-x`: no cruzar a otros FS (evita contar /proc, /mnt). | `du -xh -d1 /` |
| `ncdu /` | Explorador interactivo de uso de disco (muy rápido). | `ncdu -x /` |
| `find / -xdev -type f -size +500M` | Ficheros grandes concretos. | `find / -xdev -type f -size +500M -exec ls -lh {} +` |
| `find . -xdev -printf '%h\n' \| sort \| uniq -c \| sort -rn` | Directorios con más ficheros (problema de inodos). | — |

Ver [[du]].

```bash
# Espacio "fantasma": fichero borrado pero aún abierto (no libera bloques hasta cerrar el FD)
sudo lsof +L1            # columna NLINK=0
# liberar: truncar vía /proc o reiniciar el proceso dueño
: > /proc/<pid>/fd/<n>
```

---

## 3. Dispositivos, particiones y montajes

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `lsblk -f` | Árbol de discos/particiones con FS, UUID, label, punto de montaje. | `lsblk -f` |
| `lsblk -o NAME,SIZE,ROTA,DISC-GRAN,MODEL` | `ROTA=1` HDD / `0` SSD; soporte TRIM. | `lsblk -o NAME,SIZE,ROTA,TYPE,MOUNTPOINT` |
| `findmnt` | Árbol de montajes y opciones (ro, noatime, discard...). | `findmnt -t ext4,xfs` |
| `mount` / `cat /proc/mounts` | Montajes activos crudos. | `cat /proc/mounts` |
| `blkid` | UUID/LABEL/tipo de cada partición (para `/etc/fstab`). | `sudo blkid` |
| `fdisk -l` / `parted -l` | Tabla de particiones (MBR/GPT). | `sudo fdisk -l /dev/sda` |
| `lsof <mountpoint>` / `fuser -m` | Qué proceso impide desmontar (`target is busy`). | `fuser -m /mnt` |

Ver [[lsblk]] · [[blkid]] · [[(u)mount]].

---

## 4. Rendimiento y latencia de E/S

El disco puede estar sano y con espacio pero ser el cuello de botella (alto `%iowait` en [[CPU]]).

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `iostat -xz 1` | Por dispositivo: `r/s w/s`, `r_await/w_await` (latencia ms), `aqu-sz`, `%util`. | `iostat -xz 1 3` |
| `vmstat 1` | `bi`/`bo` (bloques in/out), `wa` (CPU esperando E/S). | `vmstat 1 5` |
| `sar -d -p 1 3` | Histórico por dispositivo (`await`, `%util`, `tps`). | `sar -d -p 1 3` |
| `sar -b 1 3` | Tasa global de transferencia (`tps`, `bread/s`, `bwrtn/s`). | `sar -b 1 3` |
| `pidstat -d 1` | E/S **por proceso** (`kB_rd/s`, `kB_wr/s`). | `pidstat -d 1 5` |
| `iotop -oPa` | Top interactivo de E/S por proceso. | `sudo iotop -oPa` |
| `ioping -c 10 /ruta` | Latencia puntual de un dispositivo/ruta. | `ioping -c 10 /var` |

Interpretación (ver [[iostat]]):
- **`%util` ≈ 100%** en HDD = saturado. En SSD/NVMe con paralelismo, `%util` **no** es fiable; mirar `aqu-sz` y `await`.
- **`await` alto** con `aqu-sz` alto = cola larga, el almacenamiento no da abasto.
- `r_await` ≫ `w_await` (o viceversa) orienta si el problema es lectura o escritura.

```bash
# Benchmark controlado (¡en ruta de pruebas, escribe datos!)
fio --name=randread --rw=randread --bs=4k --size=1G --runtime=30 --ioengine=libaio --direct=1
# Lectura secuencial cruda del dispositivo (no destructiva)
sudo hdparm -t /dev/sda
```

---

## 5. Salud física del disco (SMART)

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `smartctl -H /dev/sda` | Veredicto global de salud SMART (PASSED/FAILED). | `sudo smartctl -H /dev/sda` |
| `smartctl -a /dev/sda` | Todos los atributos (Reallocated_Sector_Ct, Pending, Wear_Leveling...). | `sudo smartctl -a /dev/nvme0` |
| `smartctl -t short /dev/sda` | Lanza autotest corto; resultados con `-l selftest`. | `sudo smartctl -t short /dev/sda` |
| `nvme smart-log /dev/nvme0` | SMART específico NVMe (percentage_used, media_errors). | `sudo nvme smart-log /dev/nvme0` |
| `badblocks -sv /dev/sdX` | Escaneo de sectores defectuosos (lento; `-n` no destructivo). | `sudo badblocks -sv /dev/sdb` |

Ver [[smartctl]]. Atributos críticos a vigilar: `Reallocated_Sector_Ct`, `Current_Pending_Sector`, `Offline_Uncorrectable`, `Wear_Leveling_Count` (SSD). Cualquiera creciendo = disco muriendo.

---

## 6. Integridad y errores del filesystem

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `dmesg -T \| grep -iE 'ext4|xfs|i/o error|ata|nvme'` | Errores de FS/driver/dispositivo en el log del kernel. | — |
| `fsck -n /dev/sdX` | Comprobar en modo solo lectura (sin reparar). | `sudo fsck -n /dev/sdb1` |
| `fsck -y /dev/sdX` | Reparar (**desmontado**; nunca en FS montado de lectura/escritura). | `sudo fsck -y /dev/sdb1` |
| `xfs_repair -n /dev/sdX` | Equivalente para XFS (`-n` = dry run). | `sudo xfs_repair -n /dev/sda2` |
| `tune2fs -l /dev/sdX` | Parámetros ext4: contador de montajes, fecha de último fsck, inodos. | `sudo tune2fs -l /dev/sda1` |
| `mount -o remount,ro /` | Pasar a solo lectura tras detectar corrupción. | — |

Ver [[fsck]].

> Un FS que se remonta solo en **read-only** (visible en `dmesg`/`findmnt`) indica que el kernel detectó corrupción o error de E/S y se protegió. No forzar `rw`: investigar disco (SMART) antes.

---

## 7. Inodos y metadatos de un fichero

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `stat fichero` | Inodo, enlaces, tamaño, bloques, atime/mtime/ctime. | `stat /etc/passwd` |
| `df -i /ruta` | Inodos del FS que contiene la ruta. | `df -i /home` |
| `ls -i` | Número de inodo por entrada. | `ls -i` |
| `find /dir -xdev -printf '.' \| wc -c` | Cuántos ficheros hay (consumo de inodos) en un subárbol. | — |
| `lsattr` / `chattr` | Atributos extendidos ext (`i` inmutable, `a` append-only). | `lsattr fichero` |

---

## Checklist exprés

```bash
df -h          # ¿lleno por bytes?
df -i          # ¿lleno por inodos?
sudo lsof +L1  # ¿espacio retenido por ficheros borrados-abiertos?
du -xh -d1 / | sort -rh | head   # ¿qué ocupa?
iostat -xz 1 3 # ¿latencia/%util? -> disco lento
sudo smartctl -H /dev/sda        # ¿salud física?
dmesg -T | grep -i 'i/o error'   # ¿errores de FS/driver?
```

Relacionado: [[CPU]] · [[RAM-Swap]] · [[iostat]] · [[df]] · [[du]] · [[lsblk]] · [[smartctl]] · [[fsck]] · [[sar]]
