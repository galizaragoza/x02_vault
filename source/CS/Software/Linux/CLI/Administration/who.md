who muestra los usuarios actualmente conectados al sistema, leyendo del fichero `/var/run/utmp` (o del fichero indicado). Para cada sesión muestra usuario, terminal/línea, hora de inicio y, opcionalmente, host de origen. A diferencia de `last` (historial sobre `wtmp`), `who` refleja el estado presente. Sin argumentos imprime el listado básico de sesiones activas.

```
who [opciones] [fichero | ARG1 ARG2]
```

> Invocado como `who am i` (o `who mom likes`, dos argumentos cualquiera) equivale a `who -m`: solo la sesión asociada a la stdin actual.

---

## Selección de información a mostrar

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` / `--all` | Equivale a `-b -d --login -p -r -t -T -u`: toda la información disponible. | `who -a` |
| `-b` / `--boot` | Hora del último arranque del sistema. | `who -b` |
| `-d` / `--dead` | Muestra procesos muertos. | `who -d` |
| `-l` / `--login` | Procesos de login del sistema (getty en espera). | `who -l` |
| `-p` / `--process` | Procesos activos lanzados por init. | `who -p` |
| `-r` / `--runlevel` | Runlevel actual. | `who -r` |
| `-t` / `--time` | Último cambio del reloj del sistema. | `who -t` |
| `-u` / `--users` | Usuarios conectados; añade tiempo de inactividad y PID. | `who -u` |
| `-m` | Solo el nombre de host y usuario asociados a la stdin. | `who -m` |
| `-q` / `--count` | Solo nombres de login y número total de usuarios. | `who -q` |
| `-s` / `--short` | Solo nombre, línea y hora (comportamiento por defecto). | `who -s` |

---

## Formato de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-H` / `--heading` | Imprime una línea de cabecera con los nombres de columna. | `who -H` |
| `-T` / `-w` / `--mesg` / `--message` / `--writable` | Añade estado de mensajería del usuario: `+` (acepta `write`), `-` (no), `?` (indeterminado). | `who -T` |
| `--lookup` | Canonicaliza los nombres de host vía DNS. | `who --lookup` |

---

## Origen de datos, ayuda y versión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `fichero` | Lee de un fichero alternativo en lugar de `/var/run/utmp` (ej. `/var/log/wtmp`). | `who /var/log/wtmp` |
| `--help` | Muestra la ayuda. | `who --help` |
| `--version` | Muestra la versión. | `who --version` |

---

## Casos de uso comunes

```bash
# Usuarios conectados con cabecera
who -H

# Solo mi sesión actual
who am i

# Número de usuarios conectados
who -q

# Detalle completo (inactividad, PID, runlevel, arranque)
who -a

# Hora del último arranque
who -b

# Resolver hosts remotos vía DNS
who --lookup

# Extraer solo nombres de usuario únicos
who | cut -d' ' -f1 | sort -u
```
