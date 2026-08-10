vmstat (virtual memory statistics) informa sobre procesos, memoria, paginación, E/S de bloque, interrupciones y actividad de CPU, leyendo de `/proc`. Sin argumentos muestra una única línea con los promedios desde el último arranque; con un intervalo, muestra muestras periódicas que reflejan la actividad reciente. Es una herramienta de primera línea para diagnosticar cuellos de botella: distinguir si un sistema está limitado por CPU, memoria, swap o E/S de disco.

```
vmstat [opciones] [delay [count]]
```

---

## Argumentos posicionales (intervalo y conteo)

| Argumento | Descripción | Ejemplo |
|-----------|-------------|---------|
| `delay` | Segundos entre actualizaciones. La primera línea son promedios desde el arranque; las siguientes, la actividad del intervalo. | `vmstat 2` |
| `count` | Número de muestras a mostrar antes de terminar. Sin él, actualiza indefinidamente. | `vmstat 2 5` |

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` / `--active` | Muestra memoria activa/inactiva en lugar de buff/cache. | `vmstat -a` |
| `-f` / `--forks` | Número total de forks desde el arranque. Salida única. | `vmstat -f` |
| `-m` / `--slabs` | Muestra información de slabs del kernel (slabinfo). Requiere privilegios. | `vmstat -m` |
| `-n` / `--one-header` | Imprime la cabecera una sola vez (útil al muestrear). | `vmstat -n 2` |
| `-s` / `--stats` | Tabla vertical de contadores y estadísticas de memoria y eventos. | `vmstat -s` |
| `-d` / `--disk` | Estadísticas detalladas por disco (lecturas/escrituras, sectores, tiempos). | `vmstat -d` |
| `-D` / `--disk-sum` | Resumen agregado de actividad de disco. | `vmstat -D` |
| `-p <partición>` / `--partition <partición>` | Estadísticas de una partición concreta. | `vmstat -p /dev/sda1` |
| `-S <unidad>` / `--unit <unidad>` | Unidad de salida: `k`/`K` (1000/1024 bytes), `m`/`M` (1000000/1048576 bytes). | `vmstat -S m` |
| `-t` / `--timestamp` | Añade columna de marca de tiempo a cada línea. | `vmstat -t 2` |
| `-w` / `--wide` | Modo ancho: columnas más anchas para valores grandes. | `vmstat -w` |
| `-y` / `--no-first` | Omite la primera línea (promedios desde arranque), mostrando solo actividad real. | `vmstat -y 2` |
| `-V` / `--version` | Muestra la versión. | `vmstat -V` |
| `-h` / `--help` | Muestra la ayuda. | `vmstat -h` |

---

## Interpretación de la salida por defecto

Las columnas se agrupan en seis bloques:

| Bloque | Columna | Significado |
|--------|---------|-------------|
| **procs** | `r` | Procesos ejecutables esperando CPU (en cola de ejecución). Si supera el nº de núcleos de forma sostenida, hay saturación de CPU. |
| **procs** | `b` | Procesos en espera ininterrumpible (normalmente E/S). |
| **memory** | `swpd` | Memoria virtual usada (swap). |
| **memory** | `free` | Memoria libre. |
| **memory** | `buff` | Memoria usada como buffers. |
| **memory** | `cache` | Memoria usada como caché de páginas. |
| **swap** | `si` | KiB/s leídos desde swap a RAM (swap-in). |
| **swap** | `so` | KiB/s escritos de RAM a swap (swap-out). Valores altos sostenidos = thrashing. |
| **io** | `bi` | Bloques recibidos de dispositivos de bloque (lectura), bloques/s. |
| **io** | `bo` | Bloques enviados a dispositivos de bloque (escritura), bloques/s. |
| **system** | `in` | Interrupciones por segundo. |
| **system** | `cs` | Cambios de contexto por segundo. |
| **cpu** | `us` | % de tiempo de CPU en código de usuario. |
| **cpu** | `sy` | % de tiempo en código de kernel (sistema). |
| **cpu** | `id` | % de tiempo inactivo (idle). |
| **cpu** | `wa` | % de tiempo esperando E/S. Alto = cuello de botella en disco. |
| **cpu** | `st` | % de tiempo robado por el hipervisor (steal time, en VMs). |

---

## Casos de uso comunes

```bash
# Muestra única (promedios desde arranque)
vmstat

# Muestreo cada 2 segundos, indefinido, sin repetir cabecera
vmstat -n 2

# 5 muestras cada segundo en MiB con timestamp
vmstat -S M -t 1 5

# Diagnóstico de presión de swap (vigilar si/so)
vmstat 1

# Estadísticas por disco
vmstat -d

# Resumen de eventos de memoria del sistema
vmstat -s

# Omitir la primera línea para ver solo actividad real
vmstat -y 2 10
```

---

## Interpretación del reporte de disco (`-d`)

Una fila por dispositivo, agrupada en lecturas y escrituras (contadores acumulados desde el arranque).

| Bloque | Columna | Significado |
|--------|---------|-------------|
| **reads** | `total` | Lecturas completadas con éxito. |
| **reads** | `merged` | Lecturas agrupadas (fusionadas) en una sola operación. |
| **reads** | `sectors` | Sectores leídos. |
| **reads** | `ms` | Milisegundos dedicados a leer. |
| **writes** | `total` / `merged` / `sectors` / `ms` | Equivalentes para escritura. |
| **IO** | `cur` | Operaciones de E/S en curso. |
| **IO** | `sec` | Segundos dedicados a E/S. |

> `vmstat` pertenece a `procps-ng`; lee de `/proc/meminfo`, `/proc/stat` y `/proc/*/stat`. Para histórico persistente o granularidad por dispositivo/red, usar [[sar]]; para E/S de disco detallada en vivo, [[iostat]].

Relacionados: [[sar]] · [[iostat]] · [[free]] · [[top]] · [[pidstat]]
