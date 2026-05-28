tail imprime las últimas N líneas o bytes de uno o más ficheros. Sin opciones muestra las 10 últimas líneas. Su caso de uso más común es el modo de seguimiento (`-f`/`-F`) que monitoriza ficheros de log en tiempo real, leyendo los datos nuevos conforme se escriben. Con múltiples ficheros, precede cada bloque con una cabecera `==> nombre <==`.

```
tail [opciones] [fichero...]
```

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n <N>` / `--lines=<N>` | Muestra las últimas N líneas. Si N tiene prefijo `+`, muestra desde la línea N hasta el final. | `tail -n 20 file.txt` / `tail -n +5 file.txt` |
| `-c <N>` / `--bytes=<N>` | Muestra los últimos N bytes. Con prefijo `+`, desde el byte N hasta el final. Acepta sufijos `K`, `M`, `G`. | `tail -c 512 file.bin` / `tail -c +1K log` |
| `-f` / `--follow[=name]` | Sigue el fichero en tiempo real: imprime datos nuevos conforme se añaden. Por defecto sigue el descriptor de fichero (`--follow=descriptor`). | `tail -f /var/log/syslog` |
| `-F` | Como `-f` pero reabre el fichero si se renombra o elimina (útil con logrotate). Equivale a `--follow=name --retry`. | `tail -F /var/log/app.log` |
| `--retry` | Reintenta abrir el fichero si no está accesible inicialmente. | `tail --retry -f /tmp/pending.log` |
| `-s <segundos>` / `--sleep-interval=<s>` | Intervalo de polling en modo `-f` en segundos (punto flotante). Por defecto: 1.0. | `tail -f -s 0.5 /var/log/auth.log` |
| `--pid=<PID>` | Termina el modo `-f` cuando el proceso con ese PID termina. | `tail -f app.log --pid=1234` |
| `-q` / `--quiet` / `--silent` | No imprime cabeceras de fichero al procesar múltiples ficheros. | `tail -q -n 5 *.log` |
| `-v` / `--verbose` | Siempre imprime la cabecera con nombre de fichero, incluso con un solo fichero. | `tail -v -n 5 file.txt` |
| `-z` / `--zero-terminated` | Usa NUL como delimitador de línea en lugar de newline. | `tail -zn 3 file` |

---

## Casos de uso comunes

```bash
# Últimas 10 líneas (por defecto)
tail /var/log/syslog

# Últimas 50 líneas de log
tail -n 50 /var/log/auth.log

# Monitorizar log en tiempo real
tail -f /var/log/nginx/access.log

# Seguir log con reaper de logrotate
tail -F /var/log/app/app.log

# Desde la línea 100 hasta el final (skip cabecera)
tail -n +100 fichero.csv

# Monitorizar y terminar cuando el proceso padre muere
tail -f /tmp/job.log --pid=$$

# Múltiples logs en tiempo real
tail -f /var/log/syslog /var/log/auth.log

# En pipeline: últimas líneas de la salida de un comando
dmesg | tail -n 20

# Ver errores recientes
grep ERROR /var/log/app.log | tail -n 10
```
