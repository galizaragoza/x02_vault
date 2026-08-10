sar (System Activity Reporter, paquete `sysstat`) recolecta, almacena y muestra estadísticas acumuladas de actividad del sistema: CPU, memoria, paginación, swap, E/S de bloque, interrupciones, red, sistemas de ficheros y más. Trabaja en dos modos: en **tiempo real** muestreando con `interval [count]`, o **histórico** leyendo los ficheros binarios diarios que escribe el recolector `sadc` (normalmente en `/var/log/sa/saDD`, alimentado por cron o el timer `sysstat-collect`). Su valor frente a `vmstat`/`iostat` es la persistencia: permite analizar a posteriori qué pasó a una hora concreta del pasado.

```
sar [opciones] [-o fichero] [interval [count]]
sar [opciones] -f /var/log/sa/saDD [-s hh:mm:ss] [-e hh:mm:ss]
```

> Si no se da ninguna opción de selección de actividad, `sar` reporta utilización de CPU (`-u`). Sin `interval`, lee del fichero diario de hoy.

---

## Opciones de selección de actividad

Cada flag activa un informe distinto; se pueden combinar (p. ej. `-rud`).

| **Flag**            | **Función**                                                                 | **Ejemplo**           |
| ------------------- | --------------------------------------------------------------------------- | --------------------- |
| `-u [ ALL ]`        | Utilización de CPU. `ALL` añade todos los campos (`%nice`, `%irq`, `%soft`, `%steal`, `%guest`…). | `sar -u 2 5`          |
| `-q [ kw[,…]\|ALL ]`| Carga, cola de ejecución y *pressure-stall*. Keywords: `LOAD`, `CPU`, `IO`, `MEM`, `PSI`. | `sar -q 1 3`          |
| `-r [ ALL ]`        | Utilización de memoria (libre, usada, buff/cache, commit…).                 | `sar -r ALL 2`        |
| `-S`                | Utilización del espacio de swap.                                            | `sar -S 5`            |
| `-W`                | Estadísticas de *swapping* (`pswpin/s`, `pswpout/s`).                       | `sar -W 1`            |
| `-B`                | Estadísticas de paginación (`pgpgin/s`, `fault/s`, `majflt/s`, `pgscan…`).  | `sar -B 2`            |
| `-H`                | Utilización de *hugepages*.                                                 | `sar -H 5`            |
| `-b`                | Tasa global de E/S y transferencia (`tps`, `bread/s`, `bwrtn/s`, `bdscd/s`).| `sar -b 2`            |
| `-d`                | Actividad por dispositivo de bloque (una línea por disco).                  | `sar -d -p 2`         |
| `-F [ MOUNT ]`      | Estadísticas de sistemas de ficheros montados; `MOUNT` muestra el punto de montaje en vez del dispositivo. | `sar -F 5` |
| `-v`                | Estado de las tablas del kernel (inode, fichero, dentry, pty).              | `sar -v 5`            |
| `-w`                | Creación de tareas y cambios de contexto (`proc/s`, `cswch/s`).             | `sar -w 1`            |
| `-y`                | Actividad de dispositivos TTY.                                              | `sar -y 5`            |
| `-I [ SUM \| ALL ]` | Estadísticas de interrupciones. `SUM` = total; `ALL` = todas (por defecto). | `sar -I ALL 1`        |
| `-m { kw[,…]\|ALL }`| Gestión de energía. Keywords: `CPU` (frecuencia), `FAN`, `FREQ`, `IN` (voltaje), `TEMP`, `USB`, `BAT`. | `sar -m CPU,TEMP 5` |
| `-n { kw[,…]\|ALL }`| Estadísticas de red (ver tabla de keywords abajo).                          | `sar -n DEV 2`        |
| `-A`                | **Todas** las actividades. Equivale a `-bBdFHSvwWy -I ALL -P ALL -m ALL -n ALL -q ALL -r ALL -u ALL`. | `sar -A -f saDD` |

---

## Selección por procesador (`-P`)

| **Flag**                  | **Función**                                                       | **Ejemplo**         |
| ------------------------- | ----------------------------------------------------------------- | ------------------- |
| `-P { lista \| ALL }`     | Estadísticas por CPU individual. Lista de cores y rangos `0,2,4-7`. `ALL` = cada CPU más el agregado `all`. | `sar -P ALL 1 5`    |
| `-P 0,3`                  | Solo las CPUs indicadas.                                          | `sar -P 0,3 2`      |

---

## Keywords de red (`-n`)

| **Keyword** | **Informe**                                                              |
| ----------- | ------------------------------------------------------------------------ |
| `DEV`       | Tráfico por interfaz (`rxpck/s`, `txpck/s`, `rxkB/s`, `txkB/s`, `%ifutil`). |
| `EDEV`      | Errores por interfaz (`rxerr/s`, `txdrop/s`, `coll/s`…).                  |
| `NFS`       | Actividad cliente NFS.                                                    |
| `NFSD`      | Actividad servidor NFS.                                                   |
| `SOCK`      | Sockets en uso (total, `tcp`, `udp`, `raw`, `ip-frag`).                   |
| `IP` / `EIP`| Datagramas IPv4 / errores IPv4.                                           |
| `ICMP` / `EICMP` | Mensajes ICMPv4 / errores ICMPv4.                                   |
| `TCP` / `ETCP`   | Conexiones TCP / errores TCP (`atmptf/s`, `retrans/s`…).             |
| `UDP`       | Datagramas UDPv4.                                                         |
| `SOCK6` / `IP6` / `EIP6` / `ICMP6` / `EICMP6` / `UDP6` | Equivalentes IPv6.             |
| `FC`        | Tráfico de tarjetas Fibre Channel HBA.                                    |
| `SOFT`      | Procesamiento de paquetes de red por software (softnet, por CPU).         |
| `ALL`       | Todas las anteriores.                                                     |

---

## Lectura/escritura de ficheros y ventana temporal

| **Flag**                          | **Función**                                                              | **Ejemplo**                       |
| --------------------------------- | ------------------------------------------------------------------------ | --------------------------------- |
| `-o [fichero]`                    | Guarda las muestras en binario (además de mostrarlas). Por defecto, el fichero diario. | `sar -A -o /tmp/sa.bin 2 30` |
| `-f [fichero]`                    | Lee de un fichero binario en vez de muestrear. Por defecto, el diario de hoy. | `sar -u -f /var/log/sa/sa15`  |
| `-D`                              | Usa el nombre de fichero diario `saYYYYMMDD` en vez de `saDD`.            | `sar -D -f /var/log/sa/sa20260611` |
| `-s [hh:mm:ss]`                   | Hora de inicio del informe al leer fichero (por defecto 08:00:00). Admite epoch. | `sar -f saDD -s 09:00:00`     |
| `-e [hh:mm:ss]`                   | Hora de fin del informe (por defecto 18:00:00). Admite epoch.            | `sar -f saDD -e 17:30:00`         |
| `-i interval`                     | Selecciona registros en intervalos de `interval` segundos (no todos).    | `sar -u -f saDD -i 600`           |
| `-C`                              | Al leer de fichero, muestra los comentarios insertados por `sadc -C`.    | `sar -C -f saDD`                  |
| `interval [count]`                | Periodo de muestreo en segundos y nº de muestras (sin `count`, indefinido). | `sar 2 10`                     |
| `-[0-9]+`                         | Atajo: lee el fichero diario de hace N días (`-1` = ayer).               | `sar -1 -u`                       |
| `--sadc`                          | Imprime la ruta del recolector `sadc` que invocaría sar.                 | `sar --sadc`                      |

---

## Formato de salida

| **Flag**                          | **Función**                                                              | **Ejemplo**                       |
| --------------------------------- | ------------------------------------------------------------------------ | --------------------------------- |
| `-t`                              | Al leer fichero, muestra las marcas de tiempo en la hora local del creador del fichero. | `sar -t -f saDD`              |
| `-h`                              | Equivale a `--pretty --human` (salida compacta y legible para humanos).  | `sar -h -r 2`                     |
| `-p` / `--pretty`                 | Salida más legible; especialmente útil para `-d` y `-n DEV`.             | `sar -d -p 2`                     |
| `--human`                         | Tamaños en formato legible (`1.0k`, `1.2M`).                             | `sar --human -r 2`                |
| `--dec={0\|1\|2}`                 | Número de decimales en los valores (por defecto 2).                      | `sar --dec=0 -u 2`                |
| `-x`                              | Añade los valores mínimo y máximo además de la media.                    | `sar -x -u -f saDD`               |
| `-z`                              | Omite la salida de dispositivos sin actividad en el periodo.             | `sar -d -z 2`                     |
| `-j { ID\|LABEL\|PATH\|UUID\|… }` | Con `-d`, muestra nombres de dispositivo persistentes.                   | `sar -d -j LABEL 2`               |
| `-V`                              | Versión y salida.                                                        | `sar -V`                          |
| `--help`                          | Ayuda y salida.                                                          | `sar --help`                      |

---

## Filtros de selección de elementos

Restringen la salida a dispositivos/interfaces/interrupciones concretos.

| **Flag**                          | **Función**                                                              | **Ejemplo**                       |
| --------------------------------- | ------------------------------------------------------------------------ | --------------------------------- |
| `--dev=lista`                     | Limita `-d` a los dispositivos de bloque indicados (separados por coma). | `sar -d --dev=sda,nvme0n1 2`      |
| `--iface=lista`                   | Limita `-n DEV/EDEV` a las interfaces indicadas.                         | `sar -n DEV --iface=eth0,wlan0 2` |
| `--fs=lista`                      | Limita `-F` a los sistemas de ficheros / puntos de montaje indicados.    | `sar -F --fs=/,/home 5`           |
| `--int=lista`                     | Limita `-I` a las interrupciones indicadas; admite rangos `0-16,35,40-`. | `sar -I ALL --int=0-16 1`         |

---

## Interpretación: CPU (`-u`)

Porcentajes del total de tiempo de CPU agregado de todos los cores.

| Columna    | Significado                                                               |
| ---------- | ------------------------------------------------------------------------- |
| `%user`    | Tiempo en código de usuario (prioridad normal).                           |
| `%nice`    | Tiempo en código de usuario con prioridad `nice` reajustada.              |
| `%system`  | Tiempo en kernel (system calls), excluyendo irq/softirq.                  |
| `%iowait`  | CPU ociosa con E/S de disco pendiente. Alto = posible cuello de disco.    |
| `%steal`   | Tiempo robado por el hipervisor a una VM.                                 |
| `%idle`    | CPU ociosa sin E/S pendiente.                                             |
| `%irq` `%soft` `%guest` `%gnice` | (con `-u ALL`) interrupciones hard/soft, tiempo de VMs invitadas. |

---

## Interpretación: memoria (`-r`)

| Columna       | Significado                                                            |
| ------------- | --------------------------------------------------------------------- |
| `kbmemfree`   | Memoria libre (KiB).                                                   |
| `kbavail`     | Memoria disponible estimada para nuevos procesos sin entrar en swap.  |
| `kbmemused`   | Memoria usada (sin contar el kernel).                                 |
| `%memused`    | Porcentaje de memoria usada.                                          |
| `kbbuffers` / `kbcached` | Memoria como buffers / caché de páginas.                   |
| `kbcommit` / `%commit` | Memoria comprometida (carga de trabajo actual) y su % vs RAM+swap. | 
| `kbdirty`     | Memoria pendiente de escribir a disco. (`-r ALL` añade `kbanonpg`, `kbslab`, `kbkstack`, etc.) |

---

## Interpretación: E/S de bloque (`-b`)

| Columna    | Significado                                              |
| ---------- | ------------------------------------------------------- |
| `tps`      | Transferencias (peticiones) por segundo al/los disco(s).|
| `rtps` / `wtps` / `dtps` | Peticiones de lectura / escritura / descarte por segundo. |
| `bread/s` / `bwrtn/s` / `bdscd/s` | Bloques (512 B) leídos / escritos / descartados por segundo. |

---

## Casos de uso comunes

```bash
# CPU en vivo: 5 muestras cada 2 s
sar -u 2 5

# Todos los campos de CPU por core
sar -P ALL -u ALL 1 3

# Memoria + swap-in/out + paginación a la vez
sar -rWB 2

# Tráfico de red de una interfaz, legible
sar -n DEV --iface=eth0 -p 2

# E/S por disco con nombres bonitos, omitiendo discos inactivos
sar -d -p -z 2

# Revisar histórico: CPU de ayer entre las 14:00 y 15:00
sar -1 -u -s 14:00:00 -e 15:00:00

# Leer un fichero diario concreto con timestamps originales
sar -t -u -f /var/log/sa/sa11

# Capturar todo a binario cada 2 s, 60 muestras, para análisis posterior
sar -A -o /tmp/captura.sar 2 60 >/dev/null
sar -u -f /tmp/captura.sar     # …y releerlo

# Carga y presión PSI (CPU/IO/MEM saturación)
sar -q PSI,LOAD 1 5
```

> Recolección histórica: la activa el paquete `sysstat` vía el servicio/timer `sysstat-collect.timer` (o cron en `/etc/cron.d/sysstat`), que ejecuta `sadc` y deja los binarios en `/var/log/sa/`.

Relacionados: [[vmstat]] · [[iostat]] · [[pidstat]] · [[mpstat]] · [[free]] · [[top]]
