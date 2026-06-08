eza es un reemplazo moderno de `ls` escrito en Rust que añade colores por tipo de fichero, iconos Unicode (con `--icons`), soporte nativo para Git (muestra el estado de cada fichero en el repositorio), vista en árbol integrada y formateo mejorado. Es un fork activo de `exa` (que quedó sin mantenimiento en 2023). Compatible con los casos de uso habituales de `ls` pero con más información y mejor legibilidad por defecto.

```
eza [opciones] [ficheros...]
```

---

## Visualización principal

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| (sin opciones) | Lista el directorio actual con colores. | `eza` |
| `-l` / `--long` | Formato largo: permisos, propietario, grupo, tamaño, fecha, nombre. | `eza -l` |
| `-a` / `--all` | Incluye ficheros ocultos (empieza por `.`), incluyendo `.` y `..`. | `eza -a` |
| `-A` / `--almost-all` | Incluye ficheros ocultos pero excluye `.` y `..`. | `eza -A` |
| `-1` / `--oneline` | Un fichero por línea. | `eza -1` |
| `-G` / `--grid` | Formato de cuadrícula (por defecto cuando la salida es terminal). | `eza -G` |
| `-x` / `--across` | Rellena la cuadrícula de izquierda a derecha en lugar de arriba a abajo. | `eza -x` |
| `--icons[=<auto\|always\|never>]` | Muestra iconos Unicode antes del nombre (requiere fuente Nerd Font). | `eza --icons` |
| `--no-icons` | Desactiva los iconos explícitamente. | `eza --no-icons` |
| `--color=<always\|auto\|never>` | Control de colores ANSI. Por defecto: `auto`. | `eza --color=never` |
| `--color-scale[=<all\|size>]` | Colorea los valores de tamaño en escala gradiente para distinguir magnitudes. | `eza -l --color-scale` |
| `--color-scale-mode=<fixed\|gradient>` | Modo de la escala de color. | `eza --color-scale --color-scale-mode=gradient` |
| `--hyperlink` | Emite los nombres de fichero como hiperenlaces OSC 8 (soportado en terminales modernos). | `eza --hyperlink -l` |

---

## Árbol de directorios

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-T` / `--tree` | Muestra el árbol de directorios recursivo (como `tree`). | `eza -T` |
| `-L <N>` / `--level=<N>` | Limita la profundidad del árbol a N niveles. | `eza -T -L 3` |

---

## Columnas en formato largo (`-l`)

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-h` / `--human-readable` | Tamaños en formato K/M/G. | `eza -lh` |
| `-b` / `--binary` | Tamaños en unidades binarias (KiB, MiB, GiB). | `eza -lb` |
| `--bytes` | Tamaños siempre en bytes. | `eza -l --bytes` |
| `-i` / `--inode` | Muestra el número de inode. | `eza -li` |
| `-H` / `--links` | Muestra el número de hard links. | `eza -lH` |
| `-S` / `--blocksize` | Muestra el tamaño en bloques asignados en disco. | `eza -lS` |
| `-o` / `--octal-permissions` | Muestra los permisos en octal además del formato simbólico. | `eza -lo` |
| `--no-permissions` | Oculta la columna de permisos. | `eza -l --no-permissions` |
| `--no-filesize` | Oculta la columna de tamaño. | `eza -l --no-filesize` |
| `--no-user` | Oculta la columna de propietario. | `eza -l --no-user` |
| `--no-time` | Oculta la columna de fecha/hora. | `eza -l --no-time` |
| `--group` | Muestra la columna de grupo (en algunos sistemas no se muestra por defecto). | `eza -l --group` |
| `--smart-group` | Oculta la columna de grupo si coincide con el propietario. | `eza -l --smart-group` |
| `-n` / `--numeric` | Muestra UID y GID numéricos en lugar de nombres. | `eza -ln` |
| `-@` / `--extended` | Muestra los atributos extendidos (xattrs) del fichero. | `eza -l@` |
| `--context` | Muestra el contexto de seguridad (SELinux). | `eza -l --context` |

---

## Tiempo y fechas

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-m` / `--modified` | Usa el tiempo de modificación (mtime). Por defecto. | `eza -lm` |
| `-u` / `--accessed` | Muestra el tiempo de último acceso (atime). | `eza -lu` |
| `-c` / `--changed` | Muestra el tiempo de cambio de estado (ctime). | `eza -lc` |
| `--created` | Muestra el tiempo de creación (btime, si el sistema lo soporta). | `eza -l --created` |
| `--time-style=<estilo>` | Formato de fecha: `default`, `iso`, `long-iso`, `full-iso`, `relative`, o cadena de formato (`+%Y-%m-%d`). | `eza -l --time-style=long-iso` |

---

## Ordenación

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-s <campo>` / `--sort=<campo>` | Campo de ordenación: `name`, `Name` (case-insensitive), `size`, `extension`, `Extension`, `modified`, `accessed`, `created`, `inode`, `type`, `none`. | `eza -l --sort=size` |
| `-r` / `--reverse` | Invierte el orden. | `eza -lr --sort=size` |
| `--group-directories-first` | Muestra directorios antes que ficheros. | `eza -l --group-directories-first` |

---

## Filtrado

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-R` / `--recurse` | Lista recursivamente todos los subdirectorios. | `eza -lR` |
| `-D` / `--only-dirs` | Muestra solo directorios. | `eza -lD` |
| `-f` / `--only-files` | Muestra solo ficheros (no directorios). | `eza -lf` |
| `--show-symlinks` | En modo árbol, sigue y muestra el destino de los symlinks. | `eza -T --show-symlinks` |
| `-I <patrón>` / `--ignore-glob=<patrón>` | Excluye ficheros que coincidan con el patrón glob. Separar múltiples con `\|`. | `eza -l -I "*.log\|*.tmp"` |

---

## Integración Git

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--git` | Muestra el estado Git de cada fichero junto a los permisos (staging + working tree). | `eza -l --git` |
| `--git-ignore` | Oculta ficheros que están en `.gitignore`. | `eza --git-ignore` |
| `--git-repos` | Muestra el estado del repositorio Git de cada subdirectorio (rama, estado). | `eza -l --git-repos` |
| `--git-repos-no-status` | Como `--git-repos` pero sin el estado (más rápido en repos grandes). | `eza -l --git-repos-no-status` |

**Estados Git** (columna `--git`): `N` nuevo, `M` modificado, `D` borrado, `R` renombrado, `T` tipo cambiado, `I` ignorado, `-` sin cambios.

---

## Casos de uso comunes

```bash
# Vista básica con colores e iconos
eza --icons

# Formato largo legible
eza -lh

# Con estado Git
eza -l --git

# Árbol de proyecto
eza -T -L 3 --icons

# Árbol con detalles
eza -lT -L 2

# Ordenar por tamaño descendente
eza -lh --sort=size -r

# Directorios primero, con iconos
eza -l --group-directories-first --icons

# Solo directorios
eza -lD

# Ver fechas en formato ISO
eza -l --time-style=long-iso

# Excluir ficheros de log y temporales
eza -l -I "*.log|*.tmp|*.o"

# Recursivo con límite de profundidad
eza -lR -L 2

# Mostrar atributos extendidos
eza -l@

# Permisos en octal
eza -lo

# Listar con color pero sin iconos (para scripts)
eza --color=always --no-icons -l
```
