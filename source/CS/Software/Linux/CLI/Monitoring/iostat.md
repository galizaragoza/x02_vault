iostat (paquete `sysstat`) reporta estadísticas de utilización de CPU y de actividad I/O de dispositivos de bloque y particiones. El primer reporte muestra promedios desde el arranque del sistema; los reportes siguientes (con `interval`) cubren el intervalo transcurrido desde el reporte anterior. Es la herramienta base para detectar cuellos de botella de almacenamiento (saturación de cola, latencia, throughput).

```
iostat [opciones] [interval [count]]
```

---

## Opciones

| **Parámetro**          | **Función**                                  | **Ejemplo**            |
| ------------------ | ---------------------------------------- | ------------------ |
| `-c`               | Muestra reporte CPU.                     | `iostat -c`        |
| `-d`               | Muestra reporte dispositivos.            | `iostat -d`        |
| `-k`               | Estadísticas en KiB/s.                   | `iostat -k`        |
| `-m`               | Estadísticas en MiB/s.                   | `iostat -m`        |
| `-p device`        | Estadísticas de dispositivo/particiones. | `iostat -p sda`    |
| `-x`               | Estadísticas extendidas.                 | `iostat -x`        |
| `-s`               | Versión corta.                           | `iostat -s`        |
| `-t`               | Agrega timestamp.                        | `iostat -t`        |
| `-y`               | Omite primer reporte.                    | `iostat -y`        |
| `--human`          | Formato legible.                         | `iostat --human`   |
| `--compact`        | Métricas en una línea.                   | `iostat --compact` |
| `--pretty`         | Salida tabular más legible para `-x`.    | `iostat -x --pretty` |
| `-z`               | Omite dispositivos sin actividad en el intervalo. | `iostat -xz 2` |
| `-N`               | Muestra nombres de mapeo de Device Mapper / LVM2. | `iostat -N` |
| `-j ID`            | Nomenclatura de dispositivo persistente (`ID`, `LABEL`, `PATH`, `UUID`). | `iostat -j ID` |
| `-g grupo`         | Agrupa varios dispositivos bajo un nombre y muestra su total. | `iostat -g misk sda sdb` |
| `-H`               | Solo muestra la línea de total del grupo (`-g`). | `iostat -g g1 -H sda sdb` |
| `-o JSON`          | Salida en formato JSON. | `iostat -o JSON` |
| `--dec=N`          | Número de decimales (0–2). | `iostat -x --dec=0` |
| `-f dir` / `+f dir`| Usa `dir` como ruta a `/proc` en vez de `/proc` (o la añade). | `iostat -f /mnt/proc` |
| `-V`               | Versión y salida. | `iostat -V` |
| `interval [count]` | Intervalo (s) y nº de reportes. Sin `count`, repite indefinidamente. | `iostat -d 2 5` |

---

## Reporte de CPU (`-c`)

Porcentajes promediados sobre todas las CPUs. En SMP, valores normalizados al total de cores.

| Columna | Descripción |
|---------|-------------|
| `%user` | Tiempo de CPU ejecutando código de usuario (prioridad normal). |
| `%nice` | Tiempo de CPU en procesos de usuario con prioridad `nice` (reajustada). |
| `%system` | Tiempo de CPU en el kernel (system calls). |
| `%iowait` | Tiempo de CPU ociosa mientras había una petición de I/O de disco pendiente. |
| `%steal` | Tiempo robado por el hipervisor para otra VM (entornos virtualizados). |
| `%idle` | Tiempo de CPU ociosa sin peticiones de I/O pendientes. |

> `%iowait` alto + `%idle` bajo sugiere cuello de botella en disco; `%iowait` alto con `%idle` alto no implica saturación.

---

## Reporte de dispositivo simple (`-d`)

| Columna | Descripción |
|---------|-------------|
| `Device` | Nombre del dispositivo o partición. |
| `tps` | Transferencias (peticiones I/O) por segundo enviadas al dispositivo. |
| `kB_read/s` / `kB_wrtn/s` | KiB leídos / escritos por segundo. |
| `kB_dscd/s` | KiB descartados (TRIM/discard) por segundo. |
| `kB_read` / `kB_wrtn` / `kB_dscd` | KiB totales leídos / escritos / descartados. |

> Con `-m` las columnas pasan a `MB/s`; con `-k` a `kB/s` (por defecto en kernels recientes). Con `-h`/`--human` se añaden sufijos legibles.

---

## Reporte extendido (`-x`)

Métricas detalladas por dispositivo. Divididas en operaciones de lectura (`r`), escritura (`w`) y descarte (`d`).

| Columna | Descripción |
|---------|-------------|
| `r/s`, `w/s`, `d/s` | Lecturas / escrituras / descartes completados por segundo. |
| `rkB/s`, `wkB/s`, `dkB/s` | KiB leídos / escritos / descartados por segundo. |
| `rrqm/s`, `wrqm/s`, `drqm/s` | Peticiones fusionadas (merged) por segundo encoladas al dispositivo. |
| `%rrqm`, `%wrqm`, `%drqm` | Porcentaje de peticiones fusionadas antes de enviarse. |
| `r_await`, `w_await`, `d_await` | Latencia media (ms) de cada tipo de petición: espera en cola + servicio. |
| `rareq-sz`, `wareq-sz`, `dareq-sz` | Tamaño medio (KiB) de las peticiones de cada tipo. |
| `aqu-sz` | Longitud media de la cola de peticiones (antes `avgqu-sz`). |
| `f/s` | Operaciones flush por segundo completadas. |
| `f_await` | Latencia media (ms) de las operaciones flush. |
| `%util` | Porcentaje de tiempo de CPU en que el dispositivo tuvo I/O en curso (saturación). Cercano a 100% = dispositivo saturado (no fiable en SSD/NVMe con paralelismo). |

---

⬇️## Casos de uso comunes

```bash
# CPU + discos, intervalo 2s, en MiB/s
iostat -m 2

# Extendido solo de un disco, sin la línea de arranque, omitiendo inactivos
iostat -xz -p sda 2

# Reporte legible y bonito con timestamp
iostat -x --pretty --human -t 5

# Solo CPU
iostat -c 1

# Nombres LVM/Device Mapper
iostat -xN 2
```