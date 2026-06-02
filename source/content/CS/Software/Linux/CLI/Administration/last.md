last muestra el historial de inicios y cierres de sesión leyendo el fichero binario `/var/log/wtmp` (por defecto), listando del más reciente al más antiguo. Cada línea indica usuario, terminal/tty, host de origen, e intervalo de la sesión. La pseudo-entrada `reboot` marca los arranques del sistema. Es una herramienta forense básica para auditar accesos: quién entró, desde dónde y cuándo. La variante `lastb` muestra los intentos de login fallidos desde `/var/log/btmp`.

```
last [opciones] [usuario...] [tty...]
lastb [opciones]
```

---

## Selección de origen de datos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-f <fichero>` / `--file <fichero>` | Lee de un fichero wtmp alternativo en lugar de `/var/log/wtmp`. | `last -f /var/log/wtmp.1` |
| `usuario` | Filtra las sesiones de un usuario concreto (argumento posicional). | `last root` |
| `tty` | Filtra por terminal (ej. `tty1`, `pts/0`). | `last pts/0` |

---

## Límite y rango temporal

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-<número>` / `-n <número>` / `--limit <número>` | Limita la salida a las N últimas líneas. | `last -10` / `last -n 10` |
| `-s <hora>` / `--since <hora>` | Muestra sesiones desde la hora indicada. | `last -s yesterday` |
| `-t <hora>` / `--until <hora>` | Muestra sesiones hasta la hora indicada. | `last -t 2026-06-01` |
| `-p <hora>` / `--present <hora>` | Muestra quién estaba conectado en ese instante exacto. | `last -p 2026-06-01T12:00:00` |

Formatos de hora aceptados: `YYYYMMDDhhmmss`, `YYYY-MM-DD hh:mm:ss`, `YYYY-MM-DD` (00:00:00), `hh:mm:ss` (hoy), `hh:mm`, `now`, `today`, `yesterday`, `tomorrow`.

---

## Formato de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` / `--hostlast` | Muestra el nombre de host en la última columna (útil con nombres largos). | `last -a` |
| `-d` / `--dns` | Traduce las IP a nombres de host vía DNS. | `last -d` |
| `-i` / `--ip` | Muestra el host de origen como dirección IP numérica. | `last -i` |
| `-R` / `--nohostname` | Omite la columna de host. | `last -R` |
| `-F` / `--fulltimes` | Muestra fechas y horas completas de login y logout. | `last -F` |
| `-w` / `--fullnames` | Muestra nombres completos de usuario y dominio sin truncar. | `last -w` |
| `-x` / `--system` | Incluye entradas de apagado del sistema y cambios de runlevel. | `last -x` |
| `--time-format <formato>` | Formato de hora: `notime`, `short`, `full`, `iso`. | `last --time-format iso` |

---

## Ayuda y versión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-h` / `--help` | Muestra la ayuda. | `last --help` |
| `-V` / `--version` | Muestra la versión. | `last --version` |

---

## Casos de uso comunes

```bash
# Últimos 10 accesos al sistema
last -n 10

# Historial de arranques del sistema
last reboot

# Sesiones de un usuario concreto con fecha completa
last -F root

# Accesos desde ayer mostrando IP numérica
last -i -s yesterday

# Intentos de login fallidos (requiere root y /var/log/btmp)
sudo lastb

# Quién estaba conectado en un instante dado
last -p 2026-06-01T03:00:00

# Auditar accesos remotos (filtrar pts y resolver DNS)
last -d | grep 'pts/'
```
