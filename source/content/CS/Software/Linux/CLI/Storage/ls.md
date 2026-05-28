ls lista el contenido de directorios y muestra información sobre ficheros. Es el comando más usado en la línea de comandos Unix/Linux. Sin argumentos lista el directorio actual; con `-l` muestra permisos, propietario, grupo, tamaño y timestamps. Los ficheros ocultos (nombre empieza con `.`) no se muestran por defecto.

```
ls [opciones] [fichero/directorio...]
```

---

## Visualización y formato

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` | Formato largo: permisos, hard links, propietario, grupo, tamaño, fecha, nombre. | `ls -l /etc` |
| `-a` / `--all` | Muestra todos los ficheros incluyendo ocultos (`.` y `..`). | `ls -a ~` |
| `-A` / `--almost-all` | Como `-a` pero no muestra `.` ni `..`. | `ls -A ~` |
| `-h` / `--human-readable` | Con `-l`, muestra tamaños en K/M/G en lugar de bytes. | `ls -lh` |
| `-H` / `--dereference-command-line` | Con symlinks como argumentos, muestra el fichero apuntado. | `ls -lH /enlace` |
| `-1` | Un fichero por línea (útil para scripts). | `ls -1 /etc` |
| `-m` | Nombres separados por comas. | `ls -m /bin` |
| `-F` / `--classify` | Añade indicador al nombre según tipo: `/` directorio, `*` ejecutable, `@` symlink, `|` FIFO, `=` socket. | `ls -F` |
| `-p` | Añade `/` solo a directorios. | `ls -p` |
| `--color[=CUANDO]` | Colorea por tipo de fichero. `CUANDO`: `always`, `auto`, `never`. | `ls --color=auto` |
| `-i` / `--inode` | Muestra el número de inode de cada fichero. | `ls -li /home` |
| `-n` / `--numeric-uid-gid` | Como `-l` pero con UID/GID numéricos en lugar de nombres. | `ls -n /etc` |
| `-o` | Como `-l` pero omite el grupo. | `ls -o /etc` |
| `-g` | Como `-l` pero omite el propietario. | `ls -g /etc` |
| `-s` / `--size` | Muestra el tamaño en bloques asignados junto al nombre. | `ls -s /usr/bin` |
| `-d` / `--directory` | Lista el directorio en sí, no su contenido (útil con `-l` para ver permisos del dir). | `ls -ld /etc` |

---

## Ordenación

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t` | Ordena por tiempo de modificación (más reciente primero). | `ls -lt /var/log` |
| `-S` | Ordena por tamaño (mayor primero). | `ls -lS /usr/lib` |
| `-X` | Ordena por extensión. | `ls -lX /etc` |
| `-v` | Ordenación natural de versiones (1.10 > 1.9). | `ls -lv releases/` |
| `-r` / `--reverse` | Invierte el orden. | `ls -ltr /var/log` |
| `-u` | Ordena por tiempo de último acceso (con `-t`). Muestra atime con `-l`. | `ls -ltu /home` |
| `-c` | Ordena por tiempo de cambio de estado (ctime). Muestra ctime con `-l`. | `ls -ltc /etc` |
| `--sort=WORD` | Controla ordenación explícitamente: `none`, `size`, `time`, `version`, `extension`. | `ls --sort=size` |
| `--group-directories-first` | Muestra directorios antes que ficheros. | `ls -l --group-directories-first` |

---

## Recursividad y profundidad

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-R` / `--recursive` | Lista recursivamente todos los subdirectorios. | `ls -R ~` |

---

## Casos de uso comunes

```bash
# Listado detallado con tamaños legibles
ls -lh

# Mostrar ficheros ocultos en formato largo
ls -la ~

# Ordenar por fecha de modificación (más reciente arriba)
ls -lt /var/log

# Ordenar por fecha inversa (más antiguo arriba)
ls -ltr /var/log

# Listar solo directorio actual sin entrar en él
ls -ld /etc/nginx/

# Solo nombres, uno por línea (para scripts)
ls -1 /usr/bin

# Ver número de inode (para encontrar hard links)
ls -li /home/usuario/

# Ordenar por tamaño (mayor primero)
ls -lSh /var/cache

# Directorios primero
ls -l --group-directories-first ~/Documentos

# Ver permisos del directorio en sí
ls -ld /var/www/html

# Buscar el fichero más reciente
ls -lt /var/log | head -5
```
