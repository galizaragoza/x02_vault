`stat` muestra los metadatos que el kernel asocia a un fichero o a un sistema de ficheros, leídos directamente del **inodo** (llamada `stat(2)`/`statx(2)`). A diferencia de `ls -l`, expone toda la información cruda: número de inodo, bloques asignados, tamaño de bloque, dispositivo, contexto SELinux y las cuatro marcas de tiempo (acceso, modificación de datos, cambio de estado y creación/birth). Es la herramienta de referencia para inspeccionar permisos, propiedad, enlaces duros y timestamps con precisión de nanosegundos, y admite formatos de salida totalmente personalizables. Forma parte de GNU coreutils.

```
stat [OPCIÓN]... FICHERO...
```

> Por defecto imprime un bloque legible por humano por cada fichero. Las opciones `-c`/`--printf` permiten emitir solo los campos deseados, lo que lo hace idóneo para scripting. Véase también [[inodos-archivos-directorios]], [[ls]], [[LinuxFileSystems]].

---

# Opciones generales

| Opción | Función | Ejemplo |
|--------|---------|---------|
| `-L`, `--dereference` | Sigue los enlaces simbólicos: informa del fichero destino en vez del propio enlace. | `stat -L /etc/localtime` |
| `-f`, `--file-system` | Informa del **sistema de ficheros** que contiene el fichero, no del fichero. Cambia el juego de secuencias de formato (ver tabla específica). | `stat -f /home` |
| `-c FORMAT`, `--format=FORMAT` | Usa `FORMAT` en lugar de la salida por defecto; añade un salto de línea tras cada fichero. No interpreta escapes de barra invertida. | `stat -c '%n %s' *.log` |
| `--printf=FORMAT` | Como `--format` pero **interpreta** escapes (`\n`, `\t`, `\\`) y **no** añade salto de línea final. | `stat --printf='%n\t%s\n' file` |
| `-t`, `--terse` | Salida en una sola línea, campos separados por espacios (formato compacto pensado para parsers). | `stat -t /etc/passwd` |
| `--cached=MODE` | Controla el uso de atributos cacheados vía `statx(2)`: `always`, `never` o `default`. Útil en sistemas de ficheros de red/FUSE para forzar lectura fresca. | `stat --cached=never /mnt/nfs/file` |
| `--help` | Muestra la ayuda y la lista completa de secuencias de formato. | `stat --help` |
| `--version` | Muestra la versión de coreutils. | `stat --version` |

---

# Secuencias de formato — fichero

Válidas con `-c`/`--printf` en el modo por defecto (estado de fichero). El flag `#` y `0` de `printf` aplican a `%a` (p. ej. `%#a` → `0644`).

| Secuencia | Función | Ejemplo |
|-----------|---------|---------|
| `%a` | Bits de permiso en octal. | `stat -c '%a' f` → `644` |
| `%A` | Permisos y tipo en forma legible (estilo `ls -l`). | `stat -c '%A' f` → `-rw-r--r--` |
| `%b` | Número de bloques asignados (cuéntese junto con `%B`). | `stat -c '%b' f` |
| `%B` | Tamaño en bytes de cada bloque reportado por `%b`. | `stat -c '%B' f` → `512` |
| `%C` | Cadena de contexto de seguridad SELinux. | `stat -c '%C' f` |
| `%d` | Número de dispositivo en decimal (`st_dev`). | `stat -c '%d' f` |
| `%D` | Número de dispositivo en hexadecimal (`st_dev`). | `stat -c '%D' f` |
| `%Hd` | Número de dispositivo mayor (major) en decimal. | `stat -c '%Hd' f` |
| `%Ld` | Número de dispositivo menor (minor) en decimal. | `stat -c '%Ld' f` |
| `%f` | Modo en bruto, en hexadecimal. | `stat -c '%f' f` |
| `%F` | Tipo de fichero descriptivo (`regular file`, `directory`, `symbolic link`…). | `stat -c '%F' /dev/sda` → `block special file` |
| `%g` | GID del grupo propietario. | `stat -c '%g' f` |
| `%G` | Nombre del grupo propietario. | `stat -c '%G' f` |
| `%h` | Número de enlaces duros (hard links). | `stat -c '%h' f` |
| `%i` | Número de inodo. | `stat -c '%i' f` |
| `%m` | Punto de montaje del fichero. | `stat -c '%m' /home/user/x` → `/home` |
| `%n` | Nombre del fichero tal como se pasó. | `stat -c '%n' *.txt` |
| `%N` | Nombre entrecomillado; si es symlink, muestra `'enlace' -> 'destino'`. | `stat -c '%N' /etc/localtime` |
| `%o` | Tamaño óptimo de bloque para E/S (hint de transferencia). | `stat -c '%o' f` |
| `%s` | Tamaño total en bytes. | `stat -c '%s' f` |
| `%r` | Número de dispositivo del nodo (`st_rdev`) en decimal. | `stat -c '%r' /dev/null` |
| `%R` | Número de dispositivo del nodo (`st_rdev`) en hexadecimal. | `stat -c '%R' /dev/null` |
| `%Hr` | Major de `st_rdev` en decimal (ficheros de dispositivo carácter/bloque). | `stat -c '%Hr' /dev/null` → `1` |
| `%Lr` | Minor de `st_rdev` en decimal. | `stat -c '%Lr' /dev/null` → `3` |
| `%t` | Major de `st_rdev` en hexadecimal. | `stat -c '%t' /dev/sda` |
| `%T` | Minor de `st_rdev` en hexadecimal. | `stat -c '%T' /dev/sda` |
| `%u` | UID del propietario. | `stat -c '%u' f` |
| `%U` | Nombre del usuario propietario. | `stat -c '%U' f` |
| `%w` | Fecha de creación/birth legible; `-` si el FS no la registra. | `stat -c '%w' f` |
| `%W` | Creación/birth en segundos desde Epoch; `0` si se desconoce. | `stat -c '%W' f` |
| `%x` | Último acceso (atime), legible. | `stat -c '%x' f` |
| `%X` | Último acceso en segundos desde Epoch. | `stat -c '%X' f` |
| `%y` | Última modificación de datos (mtime), legible. | `stat -c '%y' f` |
| `%Y` | Última modificación de datos en segundos desde Epoch. | `stat -c '%Y' f` |
| `%z` | Último cambio de estado del inodo (ctime), legible. | `stat -c '%z' f` |
| `%Z` | Último cambio de estado en segundos desde Epoch. | `stat -c '%Z' f` |

---

# Secuencias de formato — sistema de ficheros (`-f`)

Válidas únicamente con `-f`/`--file-system`. Reemplazan al juego de fichero.

| Secuencia | Función | Ejemplo |
|-----------|---------|---------|
| `%a` | Bloques libres disponibles para usuarios sin privilegios. | `stat -f -c '%a' /` |
| `%b` | Total de bloques de datos del sistema de ficheros. | `stat -f -c '%b' /` |
| `%c` | Total de nodos (inodos) del sistema de ficheros. | `stat -f -c '%c' /` |
| `%d` | Nodos (inodos) libres del sistema de ficheros. | `stat -f -c '%d' /` |
| `%f` | Bloques libres del sistema de ficheros (incl. reservados). | `stat -f -c '%f' /` |
| `%i` | ID del sistema de ficheros en hexadecimal. | `stat -f -c '%i' /` |
| `%l` | Longitud máxima de nombre de fichero. | `stat -f -c '%l' /` → `255` |
| `%n` | Nombre del fichero pasado como argumento. | `stat -f -c '%n' /` |
| `%s` | Tamaño de bloque (el usado para transferencias rápidas). | `stat -f -c '%s' /` |
| `%S` | Tamaño de bloque fundamental (el usado en los conteos de bloques). | `stat -f -c '%S' /` |
| `%t` | Tipo de sistema de ficheros en hexadecimal. | `stat -f -c '%t' /` |
| `%T` | Tipo de sistema de ficheros en forma legible. | `stat -f -c '%T' /` → `ext2/ext3` |

---

# Recetas prácticas

| Objetivo | Comando |
|----------|---------|
| Permisos en octal de un fichero (con cero inicial). | `stat -c '%#a' fichero` |
| Comparar mtime de dos ficheros en script. | `[ "$(stat -c %Y a)" -gt "$(stat -c %Y b)" ] && echo "a más nuevo"` |
| Listar nombre + tamaño + propietario en columnas. | `stat --printf='%n\t%s\t%U\n' *` |
| Ver las cuatro marcas de tiempo de golpe. | `stat -c $'%n\nacceso:  %x\nmod:     %y\ncambio:  %z\nbirth:   %w' f` |
| Saber el punto de montaje real de una ruta. | `stat -c '%m' /var/log/syslog` |
| Detectar si un FS soporta birth time. | `stat -c '%w' f` (devuelve `-` si no) |
| Mayor/menor de un dispositivo de bloque. | `stat -c '%Hr:%Lr' /dev/sda1` |
| Inspeccionar el sistema de ficheros que aloja `/var`. | `stat -f /var` |
