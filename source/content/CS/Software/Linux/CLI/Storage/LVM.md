LVM (Logical Volume Manager) abstrae el almacenamiento físico en tres capas: **Physical Volumes** (PV, discos o particiones inicializados), **Volume Groups** (VG, agrupaciones de PVs que forman un pool de espacio) y **Logical Volumes** (LV, volúmenes flexibles tallados del VG sobre los que se crea el filesystem). Permite redimensionar en caliente, mover datos entre discos, snapshots y thin provisioning sin reparticionar. Cada subcomando (`pvcreate`, `vgcreate`, `lvcreate`...) es un binario propio del paquete `lvm2`.

```
pvcreate → vgcreate → lvcreate → mkfs → mount
```

> Jerarquía: `PV ⊂ VG → LV`. El espacio se asigna en unidades llamadas **extents** (PE en el PV, LE en el LV), por defecto de 4 MiB.

---

# Physical Volumes (PV)

## pvcreate
Inicializa un disco o partición como PV para uso por LVM.

| Parámetro (Flag) | Función                               | Ejemplo de Sintaxis                     |
| ---------------- | ------------------------------------- | --------------------------------------- |
| `-f`             | Fuerza la operación sin confirmación. | `pvcreate -f /dev/sdb1`                 |
| `-y`             | Responde "sí" a todas las preguntas.  | `pvcreate -y /dev/sdb1`                 |
| `--uuid`         | Especifica UUID personalizado (con `--restorefile` en recuperación). | `pvcreate --uuid abcdef-1234 /dev/sdb1` |
| `--dataalignment`| Alinea el inicio de datos a un múltiplo (rendimiento en RAID). | `pvcreate --dataalignment 1m /dev/sdb1` |
| `--metadatasize` | Tamaño del área de metadatos.         | `pvcreate --metadatasize 128k /dev/sdb1` |

## pvdisplay / pvs
Muestra información de los PVs. `pvs` da salida columnar resumida; `pvdisplay` detallada.

| Parámetro (Flag) | Función                      | Ejemplo de Sintaxis      |
| ---------------- | ---------------------------- | ------------------------ |
| `-v`             | Información verbose.         | `pvdisplay -v /dev/sdb1` |
| `-m`             | Muestra mapa de segmentos (mapeo PE→LV). | `pvdisplay -m /dev/sdb1` |
| `-C`             | Formato columnar (como `pvs`). | `pvdisplay -C`         |
| `-o campos`      | Selecciona columnas (`pvs`).  | `pvs -o pv_name,vg_name,pv_free` |

### Columnas de `pvs`
| Columna | Descripción |
|---------|-------------|
| `PV` | Nombre del dispositivo del PV. |
| `VG` | VG al que pertenece (vacío si no asignado). |
| `Fmt` | Formato de metadatos (`lvm2`). |
| `Attr` | Atributos (`a` asignable, `x` exportado, `m` faltante). |
| `PSize` | Tamaño total del PV. |
| `PFree` | Espacio libre (extents no asignados). |

## pvremove
Borra la etiqueta LVM de un PV (debe no pertenecer a ningún VG).

| Parámetro (Flag) | Función                               | Ejemplo de Sintaxis        |
| ---------------- | ------------------------------------- | -------------------------- |
| `-f`             | Fuerza la eliminación.                | `pvremove -f /dev/sdb1`    |
| `-y`             | Responde "sí" a todas las preguntas.  | `pvremove -y /dev/sdb1`    |

## pvmove
Migra los extents asignados de un PV a otro (en caliente). Útil antes de retirar un disco.

| Parámetro (Flag) | Función                               | Ejemplo de Sintaxis                  |
| ---------------- | ------------------------------------- | ------------------------------------ |
| (sin opción)     | Mueve todos los extents del PV origen.| `pvmove /dev/sdb1`                   |
| `dest`           | Especifica PV destino.                | `pvmove /dev/sdb1 /dev/sdc1`         |
| `-n LV`          | Mueve solo los extents de un LV concreto. | `pvmove -n lv01 /dev/sdb1`       |
| `-i N`           | Intervalo (s) de reporte de progreso. | `pvmove -i 5 /dev/sdb1`              |
| `-b`             | Ejecuta en segundo plano.             | `pvmove -b /dev/sdb1`               |

---

# Volume Groups (VG)

## vgcreate
Crea un VG agrupando uno o más PVs.

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-s`             | Tamaño del extent físico (PE). | `vgcreate -s 4M vg01 /dev/sdb1` |
| `-l`             | Límite máximo de LVs en el VG. | `vgcreate -l 255 vg01 /dev/sdb1` |
| `-p`             | Límite máximo de PVs en el VG. | `vgcreate -p 128 vg01 /dev/sdb1` |

## vgextend / vgreduce
Añade o quita PVs de un VG.

| Comando / Flag | Función | Ejemplo de Sintaxis |
| -------------- | ------- | ------------------- |
| `vgextend`     | Añade un PV al VG (amplía el pool). | `vgextend vg01 /dev/sdc1` |
| `vgreduce`     | Quita un PV del VG (debe estar vacío). | `vgreduce vg01 /dev/sdc1` |
| `vgreduce --removemissing` | Elimina referencias a PVs perdidos. | `vgreduce --removemissing vg01` |

## vgdisplay / vgs
Muestra información de los VGs.

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-v`             | Información verbose. | `vgdisplay -v vg01` |
| `-s`             | Solo resumen breve. | `vgdisplay -s vg01` |
| `-C`             | Formato columnar (como `vgs`). | `vgdisplay -C` |

### Columnas de `vgs`
| Columna | Descripción |
|---------|-------------|
| `VG` | Nombre del volume group. |
| `#PV` | Número de PVs que lo componen. |
| `#LV` | Número de LVs creados. |
| `#SN` | Número de snapshots. |
| `Attr` | Atributos (`w` escritura, `z` redimensionable, `n` normal). |
| `VSize` | Tamaño total del VG. |
| `VFree` | Espacio libre asignable. |

## vgchange
Cambia atributos del VG; típicamente activa/desactiva sus LVs.

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-a y`           | Activa todos los LVs del VG. | `vgchange -a y vg01` |
| `-a n`           | Desactiva todos los LVs del VG. | `vgchange -a n vg01` |
| `-x y\|n`        | Permite/inhibe el crecimiento del VG. | `vgchange -x n vg01` |

## vgremove
Elimina un VG (sus LVs deben estar eliminados o se confirmará).

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-f`             | Fuerza, eliminando también los LVs. | `vgremove -f vg01` |

---

# Logical Volumes (LV)

## lvcreate
Crea un LV tallado del espacio libre de un VG.

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-L`             | Tamaño absoluto (con unidad: `10G`, `512M`). | `lvcreate -L 10G -n lv01 vg01` |
| `-l`             | Tamaño en extents o porcentaje (`100%FREE`, `50%VG`). | `lvcreate -l 100%FREE -n lv01 vg01` |
| `-n`             | Nombre del LV. | `lvcreate -L 10G -n lv01 vg01` |
| `-s`             | Crea un snapshot de un LV existente (origen). | `lvcreate -s -L 1G -n snap01 /dev/vg01/lv01` |
| `-T`             | Crea un thin pool (thin provisioning). | `lvcreate -L 20G -T vg01/pool` |
| `-V`             | Tamaño virtual de un thin LV dentro de un pool. | `lvcreate -V 50G -T vg01/pool -n thinlv` |
| `-i`             | Nº de stripes (RAID0 sobre varios PVs). | `lvcreate -i 2 -L 10G -n lv01 vg01` |
| `-m`             | Nº de copias mirror (RAID1). | `lvcreate -m 1 -L 10G -n lv01 vg01` |

## lvextend
Amplía un LV (y opcionalmente su filesystem).

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-L`             | Nuevo tamaño absoluto o incremento con `+`. | `lvextend -L +5G /dev/vg01/lv01` |
| `-l`             | Tamaño/incremento en extents o porcentaje. | `lvextend -l +100%FREE /dev/vg01/lv01` |
| `-r`             | Redimensiona el filesystem automáticamente (`fsadm`). | `lvextend -L +5G -r /dev/vg01/lv01` |

## lvreduce
Reduce un LV. **Destructivo** si no se encoge antes el filesystem.

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-L`             | Nuevo tamaño absoluto o decremento con `-`. | `lvreduce -L 5G /dev/vg01/lv01` |
| `-l`             | Tamaño/decremento en extents. | `lvreduce -l -50 /dev/vg01/lv01` |
| `-r`             | Reduce primero el filesystem (`fsadm`). | `lvreduce -L 5G -r /dev/vg01/lv01` |
| `-f`             | Fuerza sin confirmación. | `lvreduce -f -L 5G /dev/vg01/lv01` |

## lvresize
Cambia el tamaño en cualquier dirección (combina extend/reduce).

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-L`             | Tamaño absoluto o relativo (`+`/`-`). | `lvresize -L 20G /dev/vg01/lv01` |
| `-r`             | Redimensiona el filesystem a la vez. | `lvresize -r -L +5G /dev/vg01/lv01` |

## lvremove
Elimina un LV (y sus datos). Debe estar desmontado.

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-f`             | Fuerza sin confirmación. | `lvremove -f /dev/vg01/lv01` |
| `-y`             | Responde "sí" a todas las preguntas. | `lvremove -y /dev/vg01/lv01` |

## lvrename
Renombra un LV.

| Sintaxis | Ejemplo |
| -------- | ------- |
| `lvrename vg lv_viejo lv_nuevo` | `lvrename vg01 lv01 datos` |
| `lvrename ruta_vieja ruta_nueva` | `lvrename /dev/vg01/lv01 /dev/vg01/datos` |

## lvconvert
Convierte el tipo de un LV (snapshot, mirror, RAID, thin).

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-m`             | Cambia el nº de mirrors (añade/quita réplicas). | `lvconvert -m 1 /dev/vg01/lv01` |
| `--type`         | Convierte a un tipo (`raid1`, `thin`, `cache`). | `lvconvert --type raid1 -m1 vg01/lv01` |
| `--merge`        | Fusiona un snapshot en su origen (rollback). | `lvconvert --merge /dev/vg01/snap01` |
| `-s`             | Convierte un LV en snapshot de otro. | `lvconvert -s vg01/lv01 vg01/snap` |

## lvdisplay / lvs
Muestra información de los LVs.

| Parámetro (Flag) | Función | Ejemplo de Sintaxis |
| ---------------- | ------- | ------------------- |
| `-v`             | Información verbose. | `lvdisplay -v /dev/vg01/lv01` |
| `-m`             | Muestra mapeo de segmentos (LE→PE). | `lvdisplay -m /dev/vg01/lv01` |
| `-C`             | Formato columnar (como `lvs`). | `lvdisplay -C` |
| `-a`             | Incluye LVs internos (pools, metadata). | `lvs -a` |

### Columnas de `lvs`
| Columna | Descripción |
|---------|-------------|
| `LV` | Nombre del logical volume. |
| `VG` | VG contenedor. |
| `Attr` | Atributos (1º tipo: `-` lineal, `s` snapshot, `m` mirror, `r` raid, `t` thin; 6º estado: `a` activo). |
| `LSize` | Tamaño del LV. |
| `Pool` | Thin pool de origen (si aplica). |
| `Origin` | LV de origen (para snapshots). |
| `Data%` | % de datos usados (thin/snapshot). |
| `Meta%` | % de metadatos usados (thin pool). |

---

## Flujo de creación completo

```bash
# 1. Inicializar discos como PV
pvcreate /dev/sdb /dev/sdc

# 2. Crear VG
vgcreate vg01 /dev/sdb /dev/sdc

# 3. Crear LV usando todo el espacio libre
lvcreate -l 100%FREE -n datos vg01

# 4. Formatear y montar
mkfs.ext4 /dev/vg01/datos
mount /dev/vg01/datos /mnt/datos

# Ampliar después en caliente +10G y crecer el FS
lvextend -L +10G -r /dev/vg01/datos

# Snapshot consistente para backup
lvcreate -s -L 2G -n snap_datos /dev/vg01/datos
```
