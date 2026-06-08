Cheatsheet compacto sobre cómo Linux representa internamente ficheros, directorios e inodos, la jerarquía estándar del sistema de ficheros (FHS) y la lectura completa de la salida de `ls -lah`. Para tipos de filesystem (ext4, xfs, btrfs...) y el árbol de directorios ampliado, ver [[LinuxFileSystems]].

---

## El inodo (inode)

Un **inodo** es la estructura de datos que el filesystem usa para describir un objeto (fichero, directorio, dispositivo...). Contiene **todos los metadatos menos el nombre**: el nombre vive en la entrada de directorio que apunta al inodo.

| Contiene el inodo | NO contiene |
|-------------------|-------------|
| Tipo de objeto y permisos (modo) | El **nombre** del fichero |
| UID y GID del propietario | La ruta/path |
| Tamaño en bytes | El contenido en sí (apunta a los bloques) |
| Timestamps `atime` / `mtime` / `ctime` | |
| Contador de hard links | |
| Punteros a los bloques de datos | |
| Número de bloques asignados | |

```bash
ls -i fichero          # número de inodo
stat fichero           # volcado completo de metadatos del inodo
df -i                  # inodos usados/libres por filesystem
find . -inum 123456    # localizar todos los nombres de un inodo
```

> Si `df` reporta "No space left on device" pero `df -h` muestra espacio libre → **inodos agotados** (`df -i`). Típico con millones de ficheros diminutos.

### Hard links vs symlinks

| | Hard link | Symbolic link (symlink) |
|--|-----------|--------------------------|
| Apunta a | El **mismo inodo** | A una **ruta** (texto) |
| Inodo | Comparte inodo (mismo nº) | Inodo propio distinto |
| Cruza filesystems | No | Sí |
| Enlazar directorios | No (salvo `.`/`..`) | Sí |
| Si se borra el original | El dato sobrevive (link count > 0) | Queda colgado (*dangling*) |
| Crear | `ln origen enlace` | `ln -s origen enlace` |

El **link count** del inodo (columna 2 de `ls -l`) es el nº de hard links que lo apuntan. El dato del fichero solo se libera cuando ese contador llega a `0` **y** ningún proceso lo tiene abierto. Ver [[ln]].

---

## ¿Qué es un fichero? — tipos

Todo en Linux es un fichero. El **primer carácter** de `ls -l` indica el tipo:

| Símbolo | Tipo | Descripción |
|---------|------|-------------|
| `-` | Regular | Datos: texto, binario, imagen... |
| `d` | Directory | Tabla nombre → nº de inodo. |
| `l` | Symlink | Enlace simbólico a otra ruta. |
| `c` | Character device | Dispositivo de E/S por carácter (`/dev/tty`, `/dev/null`). |
| `b` | Block device | Dispositivo por bloques (`/dev/sda`). |
| `p` | Named pipe (FIFO) | Pipe con nombre para IPC. |
| `s` | Socket | Socket de dominio Unix para IPC. |

```bash
file fichero     # identifica el tipo real por su contenido
stat -c '%F' x   # tipo según el inodo
```

---

## ¿Qué es un directorio?

Un directorio **no contiene ficheros**: es una tabla especial que mapea **nombres → números de inodo**. Por eso renombrar/mover dentro del mismo FS es instantáneo (solo cambia la entrada, no el dato).

- Cada directorio contiene siempre dos entradas: `.` (él mismo) y `..` (el padre).
- El **link count** de un directorio = 2 + nº de subdirectorios (cada subdir aporta su `..`).
- Permiso de **ejecución** (`x`) en un directorio = poder *atravesarlo* (entrar/acceder a sus inodos); **lectura** (`r`) = poder *listar* sus nombres.

```bash
ls -ai dir       # nombres + sus inodos (incluye . y ..)
getfacl dir      # ACLs si las hay
```

---

## Jerarquía estándar (FHS) — resumen compacto

| Ruta | Contenido |
|------|-----------|
| `/` | Raíz de todo el árbol. |
| `/bin`, `/sbin` | Binarios esenciales (usuario / administración). Hoy suelen ser symlinks a `/usr/bin`. |
| `/usr` | Programas y datos de solo lectura del sistema (`/usr/bin`, `/usr/lib`, `/usr/local`). |
| `/lib`, `/lib64` | Librerías compartidas y módulos del kernel. |
| `/etc` | Configuración del sistema (`passwd`, `fstab`, `ssh/`...). |
| `/home` | Directorios personales de usuarios. |
| `/root` | Home del superusuario. |
| `/var` | Datos variables: `log/`, `cache/`, `spool/`, `lib/`. |
| `/tmp` | Temporales volátiles (se limpian al reiniciar). |
| `/dev` | Ficheros de dispositivo (gestionados por udev). |
| `/proc` | FS virtual: procesos y kernel (`/proc/<pid>`, `/proc/cpuinfo`). |
| `/sys` | FS virtual: dispositivos y drivers del kernel (sysfs). |
| `/run` | Datos de runtime desde el arranque (PIDs, sockets). |
| `/boot` | Kernel, initramfs y bootloader (GRUB). |
| `/opt` | Software de terceros autocontenido. |
| `/mnt`, `/media` | Puntos de montaje manuales / extraíbles. |
| `/srv` | Datos servidos por servicios (web, ftp). |

> `/proc` y `/sys` no ocupan disco: son interfaces del kernel en memoria. Ver árbol ampliado en [[LinuxFileSystems]].

---

## Disección completa de `ls -lah`

`ls -l` (largo) `-a` (incluye ocultos) `-h` (tamaños legibles). Línea típica:

```
-rw-r--r--   1 juan  staff   4.0K  5 jun 14:32  notas.txt
[1]          [2][3]  [4]    [5]   [6]           [7]
```

| # | Campo | Significado |
|---|-------|-------------|
| 1 | **Modo** (10 chars) | Tipo + permisos (ver desglose abajo). |
| 2 | **Link count** | Hard links al inodo (en dirs: 2 + nº subdirectorios). |
| 3 | **Propietario** | Usuario dueño (UID). Con `-n` se ve numérico. |
| 4 | **Grupo** | Grupo dueño (GID). |
| 5 | **Tamaño** | Bytes; con `-h` en K/M/G. En dirs = tamaño de la tabla, no del contenido. |
| 6 | **Timestamp** | `mtime` por defecto; `-u` atime, `-c` ctime; `--full-time` fecha completa. |
| 7 | **Nombre** | Para symlinks: `enlace -> destino`. |

### El campo de modo, carácter a carácter

```
 -   rwx   r-x   r--
 │    │     │     │
 │    │     │     └── otros (resto del mundo)
 │    │     └──────── grupo
 │    └────────────── propietario
 └─────────────────── tipo de fichero
```

| Posición | Valores posibles | Significado |
|----------|------------------|-------------|
| 1 (tipo) | `- d l c b p s` | Tipo de objeto (tabla de tipos arriba). |
| 2-4 (user) | `r w x` o `-` | Permisos del propietario. |
| 5-7 (group) | `r w x` o `-` | Permisos del grupo. |
| 8-10 (other) | `r w x` o `-` | Permisos del resto. |

Significado de cada permiso según sea **fichero** o **directorio**:

| Permiso | En fichero | En directorio |
|---------|-----------|----------------|
| `r` | Leer contenido | Listar nombres (`ls`) |
| `w` | Modificar contenido | Crear/borrar/renombrar entradas |
| `x` | Ejecutar | Atravesar/entrar (`cd`, acceder a inodos) |

### Bits especiales (sustituyen a la `x`)

| Aparece como | Bit | Octal | Efecto |
|--------------|-----|-------|--------|
| `s` en user (`rwsr-xr-x`) | SUID | `4000` | El ejecutable corre con el UID del dueño. |
| `s` en group (`rwxr-sr-x`) | SGID | `2000` | Ejecutable: corre con GID del dueño. Directorio: ficheros nuevos heredan el grupo del dir. |
| `t` en other (`rwxrwxrwt`) | Sticky | `1000` | En directorio (`/tmp`): solo el dueño de un fichero puede borrarlo. |
| `S` / `T` (mayúscula) | bit set sin `x` | | El bit especial está activo pero falta el permiso de ejecución subyacente. |

### Notación octal de permisos

```
rwx r-x r-- → 754
 │   │   └ r--=4
 │   └──── r-x=5 (4+1)
 └──────── rwx=7 (4+2+1)
```
`r=4  w=2  x=1` → suma por grupo. Bits especiales como cuarto dígito delante (`4755` = SUID + rwxr-xr-x). Ver [[chmod]], [[chown-chgrp]], [[getfacl]].

### Marcas extra que pueden aparecer

| Marca | Dónde | Significado |
|-------|-------|-------------|
| `+` al final del modo (`-rw-r--r--+`) | tras los 10 chars | El fichero tiene **ACLs** extendidas (`getfacl`). |
| `.` al final (`-rw-r--r--.`) | tras los 10 chars | Contexto **SELinux** presente sin ACL (ver con `ls -Z`). |
| `@` (en macOS/BSD) | tras el modo | Atributos extendidos. |
| `total N` | primera línea | Suma de bloques (1 KiB) usados por las entradas listadas. |

---

## Comandos rápidos relacionados

```bash
ls -lah          # listado largo + ocultos + tamaños legibles
ls -lahi         # ídem + número de inodo
stat fichero     # metadatos completos del inodo
file fichero     # tipo real por contenido
du -sh dir       # tamaño REAL del contenido de un directorio
df -hi           # uso de espacio e inodos por filesystem
find / -inum N   # todos los nombres que comparten un inodo
```

Relacionado: [[ls]], [[LinuxFileSystems]], [[chmod]], [[chown-chgrp]], [[ln]], [[getfacl]], [[Capabilities]].
