`dmesg` examina y controla el *ring buffer* del kernel de Linux, una zona de memoria circular donde el kernel y los drivers escriben sus mensajes durante el arranque y en tiempo de ejecución. Sirve para diagnosticar fallos de hardware, detección de dispositivos, errores de drivers y eventos del subsistema del kernel. En sistemas con systemd, los mismos mensajes se duplican en el journal (`journalctl -k`), pero `dmesg` lee directamente de `/dev/kmsg`. Forma parte del paquete `util-linux`.

```
dmesg [opciones]
```

> Desde kernels recientes, leer el buffer puede requerir privilegios (`CAP_SYSLOG`) si `kernel.dmesg_restrict=1`. Ejecutar con `sudo` en ese caso.

---

## Filtrado por origen del mensaje

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-k`, `--kernel` | Muestra solo mensajes generados por el kernel. | `dmesg -k` |
| `-u`, `--userspace` | Muestra solo mensajes de espacio de usuario. | `dmesg -u` |
| `-f`, `--facility <lista>` | Restringe a una o varias *facilities* (`kern`, `user`, `mail`, `daemon`, `auth`, `syslog`, `lpr`, `news`). | `dmesg -f kern,daemon` |
| `-l`, `--level <lista>` | Restringe a uno o varios niveles (`emerg`, `alert`, `crit`, `err`, `warn`, `notice`, `info`, `debug`). | `dmesg -l err,warn` |

## Formato de salida

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-H`, `--human` | Salida legible: colores, paginación y tiempos relativos. | `dmesg -H` |
| `-J`, `--json` | Emite la salida en formato JSON. | `dmesg -J` |
| `-r`, `--raw` | Imprime el buffer en crudo, sin descodificar la cabecera de facility/level. | `dmesg -r` |
| `-x`, `--decode` | Descodifica los números de facility y level a texto legible (`kern.info`, etc.). | `dmesg -x` |
| `-L`, `--color[=cuándo]` | Colorea la salida; `cuándo` = `auto`, `always` o `never`. | `dmesg -L=always` |
| `-p`, `--force-prefix` | Fuerza el prefijo de facility/level al inicio de cada línea (útil con multilínea). | `dmesg -p` |
| `-P`, `--nopager` | No canaliza la salida a través de un pager. | `dmesg -P` |

## Marcas de tiempo (timestamps)

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-T`, `--ctime` | Timestamp en formato legible (hora del sistema). Impreciso tras suspensión/cambio de hora. | `dmesg -T` |
| `-t`, `--notime` | No imprime ninguna marca de tiempo. | `dmesg -t` |
| `-d`, `--show-delta` | Muestra el incremento de tiempo entre mensajes consecutivos. | `dmesg -d` |
| `-e`, `--reltime` | Tiempo local y delta en formato legible. | `dmesg -e` |
| `--time-format <fmt>` | Formato de tiempo: `ctime`, `reltime`, `delta`, `notime`, `iso`, `raw`. | `dmesg --time-format=iso` |

## Seguimiento en tiempo real

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-w`, `--follow` | Espera e imprime nuevos mensajes a medida que aparecen. | `dmesg -w` |
| `-W`, `--follow-new` | Solo espera nuevos mensajes; no imprime el buffer existente. | `dmesg -W` |

## Fuente de datos

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-F`, `--file <fichero>` | Lee los mensajes desde un fichero en vez del buffer del kernel. | `dmesg -F /var/log/dmesg` |
| `-S`, `--syslog` | Fuerza el uso de la interfaz syslog (`syslog(2)`) en lugar de `/dev/kmsg`. | `dmesg -S` |
| `-s`, `--buffer-size <tam>` | Define el tamaño de buffer usado al consultar (defecto 16392). | `dmesg -s 65536` |

## Buffer del kernel (limpieza)

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-c`, `--read-clear` | Imprime el buffer y a continuación lo vacía. | `sudo dmesg -c` |
| `-C`, `--clear` | Vacía el ring buffer sin imprimir nada. | `sudo dmesg -C` |

## Control de la consola del kernel

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-D`, `--console-off` | Deshabilita la impresión de mensajes en la consola. | `sudo dmesg -D` |
| `-E`, `--console-on` | Rehabilita la impresión de mensajes en la consola. | `sudo dmesg -E` |
| `-n`, `--console-level <nivel>` | Fija el nivel a partir del cual los mensajes llegan a la consola (1–8). | `sudo dmesg -n 4` |

## Información

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-h`, `--help` | Muestra la ayuda. | `dmesg -h` |
| `-V`, `--version` | Muestra la versión. | `dmesg -V` |

## Relacionados

- [[journalctl]] — equivalente vía systemd (`journalctl -k` / `--dmesg`).
- [[sysctl]] — `kernel.dmesg_restrict`, `kernel.printk` controlan acceso y verbosidad.
