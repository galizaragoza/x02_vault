# Diagnóstico de RAM y swap

#Linux #Monitoring #Troubleshooting

Guía de refresco rápido para evaluar el estado de la memoria física (RAM) y de intercambio (swap) en Linux: cuánta hay, cómo está repartida, si el sistema está bajo presión y qué proceso la consume. El objetivo es distinguir entre "poca memoria libre" (normal: el kernel cachea) y "presión de memoria real" (swap activo, reclaim, OOM).

> Concepto base: en Linux `free` ≠ `disponible`. El kernel usa la RAM ociosa como caché de página (`buff/cache`); esa memoria es reclamable al instante. La métrica que importa es **`available`**, no `free`.

---

## 1. Vista rápida del reparto

| Comando               | Para qué                                                                        | Ejemplo             |              |       |                              |
| --------------------- | ------------------------------------------------------------------------------- | ------------------- | ------------ | ----- | ---------------------------- |
| `free -h`             | Resumen legible de total/usado/libre/compartido/buff-cache/disponible y swap.   | `free -h`           |              |       |                              |
| `free -m` / `free -g` | Igual en MiB / GiB.                                                             | `free -m`           |              |       |                              |
| `free -w`             | Separa `buffers` y `cache` en columnas distintas.                               | `free -w -h`        |              |       |                              |
| `free -s N -c K`      | Refresca cada N s, K veces (monitorización ligera).                             | `free -h -s 2 -c 5` |              |       |                              |
| `cat /proc/meminfo`   | Fuente cruda y completa (MemTotal, MemAvailable, Dirty, Slab, Committed_AS...). | `grep -E 'MemTotal  | MemAvailable | Dirty | Committed_AS' /proc/meminfo` |

Columnas clave de [[free]]:
- **`available`**: estimación de memoria disponible para nuevos procesos sin tocar swap. **Es la métrica de decisión.**
- **`buff/cache`**: caché de página + buffers + slab reclaimable. No es memoria "perdida".
- **`Swap used`**: swap ocupado. Que haya swap usado no es malo per se; lo malo es swap *activo* (entradas/salidas continuas).

---

## 2. ¿Hay presión de memoria real?

Distinguir "RAM llena de caché" (sano) de "el sistema está paginando" (problema).

| Señal | Cómo verla | Interpretación |
|-------|-----------|----------------|
| Swap-in / swap-out continuo | `vmstat 1` → columnas `si`/`so` | `si`/`so` > 0 sostenido = *thrashing*, RAM insuficiente. |
| `available` cayendo a casi 0 | `free -h` repetido | El kernel ya no puede reclamar; siguiente paso es swap u OOM. |
| Reclaim agresivo | `sar -B 1` → `pgscank/s`, `pgscand/s`, `pgsteal/s` | Escaneo/robo de páginas alto = presión. |
| Presión PSI | `cat /proc/pressure/memory` | `some`/`full` avg10 elevado = tareas esperando por memoria. |

```bash
# Confirmar paginación en vivo (si/so deben quedarse en 0 en un sistema sano)
vmstat 1 5

# Contadores acumulados de swap y eventos de memoria desde el arranque
vmstat -s | grep -Ei 'swap|page'

# Pressure Stall Information (kernel >= 4.20): % de tiempo bloqueado por memoria
cat /proc/pressure/memory
```

Ver [[vmstat]] para el detalle de columnas. `sar -r`/`sar -S`/`sar -W` dan la misma información con histórico (ver [[sar]]).

---

## 3. Histórico con sar

`sar` (paquete `sysstat`) permite ver presión de memoria *en el pasado*, no solo en vivo.

| Comando | Para qué |
|---------|----------|
| `sar -r 1 3` | Utilización de memoria (`%memused`, `kbavail`, `kbcached`, `kbcommit`, `%commit`). |
| `sar -S 1 3` | Utilización del espacio de swap (`%swpused`, `kbswpfree`). |
| `sar -W 1 3` | Tasa de swapping (`pswpin/s`, `pswpout/s`). |
| `sar -B 1 3` | Estadísticas de paginación (`pgpgin/s`, `majflt/s`, `pgscan*`, `pgsteal/s`). |
| `sar -r -f /var/log/sa/saDD` | Leer datos históricos guardados de un día concreto. |

---

## 4. ¿Quién consume la memoria?

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `ps aux --sort=-%mem \| head` | Top de procesos por uso de RAM (RSS). | `ps aux --sort=-%mem \| head -10` |
| `top` / `htop` | RSS/VIRT/SHR en vivo; ordenar por memoria con `M` (en top). | `top -o %MEM` |
| `smem -tk -c "pss uss rss name"` | Reparto justo con **PSS** (memoria compartida prorrateada). Más fiable que RSS. | `smem -rs pss \| head` |
| `cat /proc/<pid>/status` | `VmRSS`, `VmSwap` por proceso. | `grep -E 'VmRSS|VmSwap' /proc/1234/status` |
| `smem -w` / `cat /proc/<pid>/smaps_rollup` | Desglose por mapeo y PSS total del proceso. | `cat /proc/1234/smaps_rollup` |

> **RSS engaña**: cuenta memoria compartida (librerías) en cada proceso, sumando de más. **PSS** (proportional set size) reparte lo compartido; úsalo para sumar consumo real. **USS** es lo que se liberaría al matar el proceso.

```bash
# Procesos que más swap usan (kernel reciente)
for f in /proc/*/status; do
  awk '/^Name|^VmSwap/{printf "%s ", $2} END{print ""}' "$f"
done | sort -k2 -rn | head

# Igual pero con cabecera y columnas alineadas (VmSwap | PID | NAME), ordenado desc.
{ printf "%11s  %6s  %s\n" "VmSwap" "PID" "NAME"; \
  for s in /proc/[0-9]*/status; do \
    awk '/^Name:/{n=$2}/^Pid:/{p=$2}/^VmSwap:/{if($2>0)printf "%8d kB  %6d  %s\n",$2,p,n}' "$s"; \
  done | sort -rn; }
```

---

## 5. Gestión y ajuste de swap

Ver [[swapon]] y [[mkswap]] para creación/activación.

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `swapon --show` | Lista swaps activos (dispositivo, tipo, tamaño, uso, prioridad). | `swapon --show` |
| `cat /proc/swaps` | Equivalente crudo. | `cat /proc/swaps` |
| `swapon /dev/sdaX` / `swapoff -a` | Activar / desactivar swap. | `sudo swapoff -a && sudo swapon -a` |
| `sysctl vm.swappiness` | Tendencia del kernel a swapear (0–100, def. 60). Bajar reduce swap anticipado. | `sudo sysctl vm.swappiness=10` |
| `sysctl vm.vfs_cache_pressure` | Presión sobre caché de inodos/dentries. | `sysctl vm.vfs_cache_pressure` |
| `zramctl` | Swap comprimido en RAM (zram) — alternativa a swap en disco. | `zramctl` |

> `swapoff -a` falla si no hay RAM libre para reabsorber lo paginado. Forzar swap a RAM: `swapoff -a && swapon -a` (vacía y rellena).

---

## 6. Memoria agotada: OOM killer

Cuando `available` llega a 0 y no hay swap utilizable, el kernel invoca el **OOM killer**.

```bash
# Ver si el OOM killer ha actuado recientemente
dmesg -T | grep -i -E 'killed process|oom|out of memory'
journalctl -k | grep -i oom

# Score de probabilidad de ser matado por un proceso (mayor = antes)
cat /proc/<pid>/oom_score
# Proteger un proceso (-1000 a 1000; -1000 = inmune)
echo -1000 | sudo tee /proc/<pid>/oom_score_adj
```

- `Committed_AS` en `/proc/meminfo` vs `CommitLimit`: si el commit se acerca al límite con `vm.overcommit_memory=2`, las `malloc` empezarán a fallar antes del OOM.
- En systemd, `systemd-cgtop -m` ordena cgroups por memoria; útil para localizar el servicio culpable.

---

## Checklist exprés

```bash
free -h                      # ¿available bajo?
vmstat 1 5                   # ¿si/so > 0? -> paginando
cat /proc/pressure/memory    # ¿avg10 alto? -> presión real
ps aux --sort=-%mem | head   # ¿quién consume?
dmesg -T | grep -i oom       # ¿hubo OOM kill?
```

Relacionado: [[CPU]] · [[Discos-Filesystems]] · [[vmstat]] · [[free]] · [[sar]]
