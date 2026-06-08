`ls` lista el contenido de directorios y muestra información sobre ficheros. Es el comando más usado en la línea de comandos Unix/Linux. Sin argumentos lista el directorio actual; con `-l` muestra permisos, propietario, grupo, tamaño y timestamps. Los ficheros ocultos (nombre empieza con `.`) no se muestran por defecto. Esta guía cubre las opciones de **GNU coreutils** `ls`.

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
| `-h` / `--human-readable` | Con `-l`, muestra tamaños en K/M/G (potencias de 1024). | `ls -lh` |
| `--si` | Tamaños legibles en potencias de 1000 (kB/MB/GB). | `ls -l --si` |
| `-H` / `--dereference-command-line` | Sigue los symlinks pasados como argumento. | `ls -lH /enlace` |
| `--dereference-command-line-symlink-to-dir` | Sigue un symlink de la CLI solo si apunta a directorio. | `ls -l --dereference-command-line-symlink-to-dir lnk` |
| `-L` / `--dereference` | Muestra info del fichero apuntado por cada symlink, no del enlace. | `ls -lL /etc/alternatives` |
| `-1` | Un fichero por línea (útil para scripts). | `ls -1 /etc` |
| `-m` | Nombres separados por comas, rellenando el ancho. | `ls -m /bin` |
| `-C` | Salida en columnas ordenada verticalmente (defecto en terminal). | `ls -C` |
| `-x` | En columnas pero ordenado horizontalmente (por filas). | `ls -x` |
| `--format=PALABRA` | Formato explícito: `across`/`horizontal`, `commas`, `long`/`verbose`, `single-column`, `vertical`. | `ls --format=single-column` |
| `-F` / `--classify` | Añade indicador según tipo: `/` dir, `*` ejecutable, `@` symlink, `\|` FIFO, `=` socket. | `ls -F` |
| `--file-type` | Como `-F` pero sin el `*` en ejecutables. | `ls --file-type` |
| `-p` / `--indicator-style=slash` | Añade `/` solo a directorios. | `ls -p` |
| `--indicator-style=ESTILO` | Estilo de indicador: `none`, `slash`, `file-type`, `classify`. | `ls --indicator-style=classify` |
| `--color[=CUÁNDO]` | Colorea por tipo de fichero. `CUÁNDO`: `always`, `auto`, `never`. | `ls --color=auto` |
| `--hyperlink[=CUÁNDO]` | Emite hyperlinks de terminal sobre los nombres. | `ls --hyperlink=auto` |
| `-i` / `--inode` | Muestra el número de inode de cada fichero. | `ls -li /home` |
| `-n` / `--numeric-uid-gid` | Como `-l` pero con UID/GID numéricos. | `ls -n /etc` |
| `-o` | Como `-l` pero omite el grupo. | `ls -o /etc` |
| `-g` | Como `-l` pero omite el propietario. | `ls -g /etc` |
| `-G` / `--no-group` | Con `-l`, no imprime la columna de grupo. | `ls -lG` |
| `--author` | Con `-l`, muestra el autor de cada fichero. | `ls -l --author` |
| `-s` / `--size` | Muestra el tamaño en bloques asignados junto al nombre. | `ls -s /usr/bin` |
| `-d` / `--directory` | Lista el directorio en sí, no su contenido. | `ls -ld /etc` |
| `--zero` | Termina cada línea con NUL en vez de salto de línea. | `ls --zero` |
| `-w` / `--width=N` | Fija el ancho de salida en N columnas (`0` = sin límite). | `ls -w 120` |
| `-T` / `--tabsize=N` | Tamaño de tabulación (defecto 8). | `ls -T 4` |
| `--block-size=TAM` | Escala tamaños a TAM (`1K`, `1M`, `K`, `MB`...). | `ls -l --block-size=M` |
| `-k` / `--kibibytes` | Tamaños y bloques en KiB; ignora `POSIXLY_CORRECT` en bloques. | `ls -lk` |

---

## Tiempos y fechas

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--full-time` | Como `-l` con fecha/hora completa estilo ISO (`-l --time-style=full-iso`). | `ls --full-time` |
| `--time=PALABRA` | Qué tiempo mostrar/ordenar: `atime`/`access`/`use`, `ctime`/`status`, `mtime`(defecto), `birth`/`creation`. | `ls -l --time=birth` |
| `--time-style=ESTILO` | Formato de fecha: `full-iso`, `long-iso`, `iso`, `locale`, o `+FORMATO` tipo `date`. | `ls -l --time-style=long-iso` |
| `-u` | Usa/ordena por tiempo de último acceso (atime). | `ls -ltu /home` |
| `-c` | Usa/ordena por tiempo de cambio de estado (ctime). | `ls -ltc /etc` |

---

## Ordenación

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t` | Ordena por tiempo de modificación (más reciente primero). | `ls -lt /var/log` |
| `-S` | Ordena por tamaño (mayor primero). | `ls -lS /usr/lib` |
| `-X` | Ordena por extensión. | `ls -lX /etc` |
| `-v` | Ordenación natural de versiones (1.10 > 1.9). | `ls -lv releases/` |
| `-U` | Sin ordenar; orden físico del directorio. | `ls -U` |
| `-r` / `--reverse` | Invierte el orden. | `ls -ltr /var/log` |
| `-f` | No ordena; equivale a `-aU` y desactiva `--color`. | `ls -f /proc` |
| `--sort=PALABRA` | Ordenación explícita: `none`, `size`, `time`, `version`, `extension`, `width`. | `ls --sort=size` |
| `--group-directories-first` | Muestra directorios antes que ficheros. | `ls -l --group-directories-first` |

---

## Filtrado de entradas

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-B` / `--ignore-backups` | Omite ficheros de backup terminados en `~`. | `ls -B` |
| `-I PATRÓN` / `--ignore=PATRÓN` | Omite entradas que casen el patrón glob. | `ls -I '*.tmp'` |
| `--hide=PATRÓN` | Como `-I` pero se anula si se usa `-a`/`-A`. | `ls --hide='*.log'` |

---

## Quoting y caracteres especiales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-b` / `--escape` | Escapa caracteres no gráficos con secuencias C (`\n`, `\t`...). | `ls -b` |
| `-q` / `--hide-control-chars` | Sustituye caracteres de control por `?` (defecto en terminal). | `ls -q` |
| `--show-control-chars` | Muestra los caracteres de control tal cual. | `ls --show-control-chars` |
| `-Q` / `--quote-name` | Encierra los nombres entre comillas dobles. | `ls -Q` |
| `--quoting-style=ESTILO` | Estilo de citado: `literal`, `shell`, `shell-always`, `shell-escape`, `c`, `escape`, `locale`, `clocale`. | `ls --quoting-style=shell-escape` |
| `-N` / `--literal` | Imprime los nombres literalmente, sin citar. | `ls -N` |

---

## SELinux y metadatos extendidos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-Z` / `--context` | Muestra el contexto de seguridad SELinux de cada fichero. | `ls -lZ /etc` |
| `--dired` / `-D` | Genera salida adicional para el modo dired de Emacs. | `ls -l --dired` |

---

## Recursividad

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-R` / `--recursive` | Lista recursivamente todos los subdirectorios. | `ls -R ~` |

---

## Información del programa

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--help` | Muestra la ayuda y termina. | `ls --help` |
| `--version` | Muestra la versión de coreutils. | `ls --version` |

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

# Listar solo el directorio actual sin entrar en él
ls -ld /etc/nginx/

# Solo nombres, uno por línea (para scripts)
ls -1 /usr/bin

# Ver número de inode (para encontrar hard links)
ls -li /home/usuario/

# Ordenar por tamaño (mayor primero)
ls -lSh /var/cache

# Directorios primero
ls -l --group-directories-first ~/Documentos

# Fecha/hora completa ISO y contexto SELinux
ls -l --full-time -Z /etc

# Nombres con caracteres raros, citados de forma segura
ls --quoting-style=shell-escape

# Excluir ficheros temporales
ls -I '*.tmp' -I '*.bak'

# Buscar el fichero más reciente
ls -lt /var/log | head -5
```

Relacionado: [[eza-better-ls]], [[tree]], [[stat]], [[lsblk]].
