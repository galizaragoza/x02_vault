# Diagnóstico de CPU

#Linux #Monitoring #Troubleshooting

Guía de refresco rápido para evaluar el estado de la CPU en Linux: cuántos núcleos hay, cuánto se usan, cómo se reparte el tiempo (usuario/sistema/iowait/steal), si la cola de ejecución está saturada y qué proceso o hilo carga la máquina. El objetivo es decidir si el sistema está realmente *CPU-bound* o si el cuello de botella es otro (E/S, memoria, contención de locks).

> Concepto base: una **load average** alta no siempre es CPU. En Linux la carga incluye procesos en estado **D** (E/S ininterrumpible). Hay que cruzar load con `%iowait` y con la cola de ejecutables (`r` de vmstat).

---

## 1. Inventario del procesador

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `lscpu` | Modelo, sockets, cores, hilos por core, cachés, flags, frecuencia, virtualización. | `lscpu` |
| `nproc` | Número de CPUs lógicas disponibles al proceso. | `nproc --all` |
| `cat /proc/cpuinfo` | Detalle por CPU lógica (MHz actual, flags). | `grep -m1 'model name' /proc/cpuinfo` |
| `lscpu -e` / `cat /sys/devices/system/cpu/online` | Topología y CPUs online/offline. | `lscpu -e=CPU,CORE,SOCKET,MHZ` |

---

## 2. Carga y vista global en vivo

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `uptime` | Load average 1/5/15 min. Comparar contra nº de cores. | `uptime` |
| `cat /proc/loadavg` | Load + nº de procesos ejecutables/total + último PID. | `cat /proc/loadavg` |
| `top` / `htop` | %CPU por proceso, %us/sy/ni/id/wa/hi/si/st global. En top: `1` separa por core, `H` muestra hilos. | `top -1` |
| `vmstat 1` | Columnas `r` (cola de ejecución), `us/sy/id/wa/st`, `cs`/`in`. | `vmstat 1 5` |
| `cat /proc/pressure/cpu` | PSI: % de tiempo con tareas esperando CPU. | `cat /proc/pressure/cpu` |

Interpretación rápida de las columnas de CPU (ver [[vmstat]] y [[top]]):
- **`us`** alto: trabajo en espacio de usuario (la app calcula). Normal si es el objetivo.
- **`sy`** alto: kernel; sospechar syscalls excesivas, drivers, red.
- **`wa`** (iowait) alto: la CPU espera disco/red → el cuello es E/S, **no** CPU. Ver [[Discos-Filesystems]].
- **`st`** (steal) alto: hipervisor robando CPU → vecino ruidoso o vCPU sobreaprovisionada (VM).
- **`r`** > nº de cores sostenido: más ejecutables que cores → saturación real de CPU.
- **`cs`** (context switches) e **`in`** (interrupts) muy altos: posible contención/thrashing de scheduler.

---

## 3. Por núcleo y por proceso/hilo

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `mpstat -P ALL 1` | %CPU desglosado por core (detecta un solo core saturado = app monohilo). | `mpstat -P ALL 1 3` |
| `pidstat 1` | %CPU por proceso a lo largo del tiempo (usr/system/guest/wait). | `pidstat -u 1 5` |
| `pidstat -t 1` | Igual pero por **hilo** (TID) — clave en apps multihilo. | `pidstat -t -p <pid> 1` |
| `ps -eLo pid,tid,pcpu,comm --sort=-pcpu` | Hilos ordenados por CPU. | `ps -eLo pid,tid,pcpu,comm --sort=-pcpu \| head` |
| `top -H -p <pid>` | Hilos de un proceso en vivo. | `top -H -p 1234` |

> Un core al 100% mientras el resto está ocioso (`mpstat -P ALL`) indica trabajo **monohilo**: no se arregla con más cores, sino con paralelización o más frecuencia.

Ver [[pidstat]].

---

## 4. Histórico y reporte con sar / mpstat

| Comando | Para qué |
|---------|----------|
| `sar -u 1 3` | Utilización global de CPU (`%user %nice %system %iowait %steal %idle`). |
| `sar -u ALL 1 3` | Desglose extendido (irq, soft, guest...). |
| `sar -P ALL 1 3` | Utilización por core. |
| `sar -q 1 3` | Cola de ejecución y load (`runq-sz`, `plist-sz`, `ldavg-1/5/15`, `blocked`). |
| `sar -w 1 3` | Creación de tareas y context switches (`proc/s`, `cwch/s`...). |
| `sar -u -f /var/log/sa/saDD` | Reproducir un día histórico (post-mortem). |

Ver [[sar]].

---

## 5. Frecuencia, governor y térmica

La CPU puede "ir lenta" sin estar saturada: throttling térmico o governor en modo ahorro.

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `cpupower frequency-info` | Governor activo, rango de frecuencia, driver. | `cpupower frequency-info` |
| `grep MHz /proc/cpuinfo` | Frecuencia instantánea por core. | `watch -n1 "grep MHz /proc/cpuinfo"` |
| `turbostat` | Frecuencia real, C-states, consumo, temperatura (Intel). | `sudo turbostat --interval 1` |
| `sensors` | Temperaturas; detectar throttling térmico. | `sensors` |
| `cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor` | Governor por core (`powersave` limita rendimiento). | — |
| `sar -m CPU 1` | Estadísticas de frecuencia/power management. | `sar -m CPU 1 3` |

```bash
# Forzar máximo rendimiento (si hay throttling por governor)
sudo cpupower frequency-set -g performance
```

---

## 6. Profiling: ¿en qué se va la CPU?

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `perf top` | Funciones que más ciclos consumen, en vivo (todo el sistema o `-p`). | `sudo perf top` |
| `perf stat <cmd>` | Contadores (IPC, cache-miss, branch-miss) de un comando. | `perf stat -d ./prog` |
| `perf record` / `perf report` | Muestreo para flamegraph / análisis offline. | `sudo perf record -g -p 1234 -- sleep 10` |
| `strace -c -p <pid>` | Resumen de syscalls (si `%sy` es alto, ver dónde). | `strace -c -p 1234` |
| `cat /proc/<pid>/stack` | Pila kernel de un proceso bloqueado. | `sudo cat /proc/1234/stack` |

> `%sy` (system) alto → perfilar syscalls con [[strace]] `-c`. `%us` alto → perfilar userland con `perf`.

---

## 7. Afinidad y prioridad

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `taskset -cp <pid>` | Ver/fijar a qué cores puede correr un proceso. | `taskset -cp 0-3 1234` |
| `nice` / `renice` | Prioridad de scheduling (−20 alta … 19 baja). | `renice +10 -p 1234` |
| `chrt` | Política de tiempo real (FIFO/RR) y prioridad. | `chrt -f 50 ./prog` |
| `ionice` | Prioridad de E/S (relacionado si la CPU espera por disco). | `ionice -c2 -n7 -p 1234` |

---

## Checklist exprés

```bash
uptime                       # load vs nproc
vmstat 1 5                   # r>cores? wa alto? st alto?
mpstat -P ALL 1 3            # ¿un solo core saturado (monohilo)?
pidstat -u 1 5               # ¿qué proceso?
top -H -p <pid>              # ¿qué hilo?
sensors; cpupower frequency-info   # ¿throttling/governor?
```

Relacionado: [[RAM-Swap]] · [[Discos-Filesystems]] · [[vmstat]] · [[pidstat]] · [[top]] · [[sar]]
