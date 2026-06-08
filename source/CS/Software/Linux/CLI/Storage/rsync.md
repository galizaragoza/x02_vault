rsync es una herramienta de sincronización y transferencia de archivos que minimiza el tráfico de red mediante el algoritmo de delta encoding: solo transfiere los bloques que han cambiado entre origen y destino. Soporta transferencias locales, remotas vía SSH o protocolo nativo rsync, y opera en modo demonio para copias programadas. Es la herramienta estándar en Unix/Linux para backups incrementales, réplicas de servidores y sincronización de directorios.

```
rsync [opciones] origen destino
rsync [opciones] origen [usuario@]host:destino
rsync [opciones] [usuario@]host:origen destino
rsync [opciones] origen rsync://[usuario@]host[:puerto]/módulo/destino
```

> **Nota sobre rutas:** `origen/` (con barra final) transfiere el *contenido* del directorio. `origen` (sin barra) transfiere el directorio entero (crea `origen/` dentro del destino).

---

## Modos de operación y transferencia

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` / `--archive` | Modo archivo: equivale a `-rlptgoD`. Preserva permisos, tiempos, propietario, grupo, dispositivos y sigue links simbólicos. | `rsync -a origen/ destino/` |
| `-r` / `--recursive` | Copia recursiva de directorios. | `rsync -r origen/ destino/` |
| `-u` / `--update` | Omite ficheros en destino que son más nuevos que en origen. | `rsync -au origen/ destino/` |
| `--existing` / `--ignore-non-existing` | Actualiza solo ficheros que ya existen en destino. | `rsync -a --existing origen/ destino/` |
| `--ignore-existing` | No sobreescribe ficheros que ya existen en destino. | `rsync -a --ignore-existing origen/ destino/` |
| `-I` / `--ignore-times` | No omite ficheros aunque timestamp y tamaño coincidan; fuerza retransferencia. | `rsync -aI origen/ destino/` |
| `-c` / `--checksum` | Compara por checksum MD4 en lugar de timestamp+tamaño para decidir si transferir. Más lento pero más fiable. | `rsync -ac origen/ destino/` |
| `--size-only` | Omite ficheros si solo difieren en timestamp (compara solo tamaño). | `rsync -a --size-only origen/ destino/` |
| `-W` / `--whole-file` | Transfiere ficheros completos, sin delta encoding. Útil en redes rápidas o discos locales. | `rsync -aW origen/ destino/` |
| `-n` / `--dry-run` | Simulación: muestra qué se haría sin ejecutar ningún cambio. | `rsync -an --delete origen/ destino/` |
| `--list-only` | Lista ficheros en origen sin transferir. | `rsync --list-only origen/` |

---

## Borrado en destino

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--delete` | Borra en destino los ficheros que no existen en origen (sincronización exacta). | `rsync -a --delete origen/ destino/` |
| `--delete-before` | Borra en destino antes de transferir. | `rsync -a --delete-before origen/ destino/` |
| `--delete-after` | Borra en destino después de transferir (por defecto con `--delete`). | `rsync -a --delete-after origen/ destino/` |
| `--delete-during` | Borra en destino a medida que se transfiere cada directorio. | `rsync -a --delete-during origen/ destino/` |
| `--delete-excluded` | Borra también en destino los ficheros que coinciden con `--exclude`. | `rsync -a --delete --delete-excluded origen/ destino/` |
| `--force` | Fuerza borrado de directorios no vacíos en destino aunque se bloqueara. | `rsync -a --delete --force origen/ destino/` |
| `--max-delete=<n>` | Aborta si el número de borrados supera `n`. Protección contra errores. | `rsync -a --delete --max-delete=100 origen/ destino/` |

---

## Filtros e inclusión/exclusión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--exclude=<patrón>` | Excluye ficheros/dirs que coincidan con el patrón glob. | `rsync -a --exclude='*.log' origen/ destino/` |
| `--include=<patrón>` | Incluye explícitamente ficheros aunque caigan bajo una exclusión. | `rsync -a --include='*.conf' --exclude='*' origen/ destino/` |
| `--exclude-from=<archivo>` | Lee patrones de exclusión desde un archivo (uno por línea). | `rsync -a --exclude-from=.rsyncignore origen/ destino/` |
| `--include-from=<archivo>` | Lee patrones de inclusión desde un archivo. | `rsync -a --include-from=include.txt --exclude='*' origen/ destino/` |
| `--filter=<regla>` | Regla de filtro avanzada. Prefijos: `+ include`, `- exclude`, `merge archivo`, `dir-merge archivo`, `protect`, `risk`. | `rsync -a --filter='- *.tmp' --filter='- .git/' origen/ destino/` |
| `--cvs-exclude` / `-C` | Excluye automáticamente ficheros ignorados por CVS/Git (`.git`, `*.o`, etc.). | `rsync -aC origen/ destino/` |
| `--max-size=<tamaño>` | No transfiere ficheros mayores que el tamaño indicado (ej. `10m`, `1g`). | `rsync -a --max-size=50m origen/ destino/` |
| `--min-size=<tamaño>` | No transfiere ficheros menores que el tamaño indicado. | `rsync -a --min-size=1k origen/ destino/` |

---

## Permisos y metadatos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-p` / `--perms` | Preserva permisos de fichero. | `rsync -ap origen/ destino/` |
| `-t` / `--times` | Preserva tiempos de modificación. | `rsync -at origen/ destino/` |
| `-o` / `--owner` | Preserva propietario (requiere root en destino). | `rsync -ao origen/ destino/` |
| `-g` / `--group` | Preserva grupo. | `rsync -ag origen/ destino/` |
| `-D` | Preserva dispositivos y ficheros especiales (equivale a `--devices --specials`). | `rsync -aD origen/ destino/` |
| `--no-perms` | No transfiere permisos. | `rsync -a --no-perms origen/ destino/` |
| `--no-owner` | No transfiere propietario. | `rsync -a --no-owner origen/ destino/` |
| `--no-group` | No transfiere grupo. | `rsync -a --no-group origen/ destino/` |
| `--chmod=<modo>` | Aplica permisos forzados a los ficheros transferidos (ej. `u+rw,g-w`). | `rsync -a --chmod=D755,F644 origen/ destino/` |
| `--numeric-ids` | No mapea UID/GID por nombre; usa los valores numéricos directamente. | `rsync -a --numeric-ids origen/ destino/` |
| `-A` / `--acls` | Preserva ACLs (POSIX). Requiere soporte en origen y destino. | `rsync -aA origen/ destino/` |
| `-X` / `--xattrs` | Preserva atributos extendidos. | `rsync -aX origen/ destino/` |

---

## Enlaces simbólicos y hard links

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` / `--links` | Copia los symlinks como symlinks. | `rsync -al origen/ destino/` |
| `-L` / `--copy-links` | Copia los ficheros/dirs referenciados por symlinks (dereferencia). | `rsync -aL origen/ destino/` |
| `--copy-unsafe-links` | Dereferencia solo los symlinks que apuntan fuera del árbol origen. | `rsync -a --copy-unsafe-links origen/ destino/` |
| `--safe-links` | Ignora symlinks que apunten fuera del árbol origen. | `rsync -a --safe-links origen/ destino/` |
| `-H` / `--hard-links` | Preserva hard links (los recrea en destino). Añade coste de memoria. | `rsync -aH origen/ destino/` |

---

## Transferencias parciales y reanudación

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-P` | Equivale a `--partial --progress`. Muestra progreso y reanuda transferencias interrumpidas. | `rsync -aP origen/ destino/` |
| `--partial` | Mantiene los ficheros parcialmente transferidos en destino para reanudar luego. | `rsync -a --partial origen/ destino/` |
| `--partial-dir=<dir>` | Guarda los ficheros parciales en un directorio temporal, no en la ubicación final. | `rsync -a --partial-dir=.rsync-tmp origen/ destino/` |
| `--append` | Añade datos al final de ficheros existentes en destino en lugar de resincronizarlos. | `rsync -a --append origen/ destino/` |
| `--append-verify` | Como `--append` pero verifica checksum del bloque existente antes de añadir. | `rsync -a --append-verify origen/ destino/` |
| `--temp-dir=<dir>` | Usa un directorio temporal en destino para ficheros en tránsito antes de moverlos al lugar final. | `rsync -a --temp-dir=/tmp/rsync origen/ destino/` |

---

## Conexión remota y red

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-e <shell>` / `--rsh=<shell>` | Shell remoto a usar. Permite pasar opciones SSH. | `rsync -e "ssh -p 2222 -i ~/.ssh/id_rsa" origen/ user@host:destino/` |
| `--rsync-path=<cmd>` | Ruta al binario rsync en el servidor remoto. | `rsync --rsync-path=/usr/local/bin/rsync origen/ user@host:destino/` |
| `--address=<ip>` | Dirección IP local a usar para la conexión de salida. | `rsync --address=192.168.1.5 origen/ user@host:destino/` |
| `--port=<puerto>` | Puerto para el demonio rsync nativo (no SSH). Por defecto: 873. | `rsync --port=8730 origen/ rsync://host/modulo/` |
| `--sockopts=<opts>` | Opciones de socket TCP (ej. `SO_SNDBUF`). | `rsync --sockopts=SO_SNDBUF=131072 origen/ destino/` |
| `--bwlimit=<KB/s>` | Limita el ancho de banda en KB/s. | `rsync --bwlimit=2000 -a origen/ destino/` |
| `--timeout=<n>` | Timeout de E/S en segundos. Sale si no hay actividad durante `n` segundos. | `rsync --timeout=60 -a origen/ user@host:destino/` |
| `--contimeout=<n>` | Timeout de conexión en segundos. | `rsync --contimeout=30 -a origen/ user@host:destino/` |
| `--password-file=<archivo>` | Fichero con la contraseña para autenticación en demonio rsync (no SSH). | `rsync --password-file=/etc/rsync.pass origen/ rsync://user@host/modulo/` |

---

## Compresión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-z` / `--compress` | Comprime datos durante la transferencia (reduce tráfico de red, aumenta CPU). | `rsync -az origen/ user@host:destino/` |
| `--compress-level=<n>` | Nivel de compresión zlib de 1 (rápido) a 9 (máximo). Por defecto: 6. | `rsync -az --compress-level=9 origen/ user@host:destino/` |
| `--skip-compress=<lista>` | No comprime ficheros con estas extensiones (ya comprimidos). Por defecto: gz,bz2,xz,zip,mp4,jpg... | `rsync -az --skip-compress=mp4/mkv origen/ destino/` |

---

## Backups

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-b` / `--backup` | Crea copia de seguridad de los ficheros sobreescritos o borrados en destino. | `rsync -ab origen/ destino/` |
| `--backup-dir=<dir>` | Directorio donde almacenar los backups (relativo al destino). | `rsync -ab --backup-dir=/backups/$(date +%Y%m%d) origen/ destino/` |
| `--suffix=<sufijo>` | Sufijo añadido a los ficheros de backup. Por defecto: `~`. | `rsync -ab --suffix=.bak origen/ destino/` |

---

## Salida y logging

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v` / `--verbose` | Muestra ficheros transferidos. Varios `-v` aumentan el detalle. | `rsync -av origen/ destino/` |
| `-q` / `--quiet` | Silencia la salida excepto errores. | `rsync -aq origen/ destino/` |
| `-h` / `--human-readable` | Muestra tamaños en formato legible (K, M, G). | `rsync -ah origen/ destino/` |
| `--progress` | Muestra progreso por fichero durante la transferencia. | `rsync -a --progress origen/ destino/` |
| `--stats` | Muestra estadísticas de la transferencia al terminar (bytes transferidos, ratio delta, etc.). | `rsync -a --stats origen/ destino/` |
| `--log-file=<archivo>` | Escribe log de la operación en un fichero. | `rsync -a --log-file=/var/log/rsync.log origen/ destino/` |
| `--log-file-format=<fmt>` | Formato de cada línea del log (acepta los mismos tokens que `--out-format`). | `rsync -a --log-file=rsync.log --log-file-format="%t %f %b" origen/ destino/` |
| `--out-format=<fmt>` / `--itemize-changes` | Formato de salida por fichero. `-i` muestra cadena de cambios (perms, tamaño, tipo). | `rsync -ai origen/ destino/` |

---

## Demonio rsync

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--daemon` | Ejecuta rsync como demonio. Lee `/etc/rsyncd.conf` o `--config`. | `rsync --daemon` |
| `--config=<archivo>` | Fichero de configuración del demonio. | `rsync --daemon --config=/etc/rsyncd.conf` |
| `--no-detach` | No hace fork al background (útil para systemd/supervisores). | `rsync --daemon --no-detach` |

---

## Casos de uso comunes

```bash
# Sincronización local básica (modo archivo, verbose, con progreso)
rsync -avP origen/ destino/

# Sincronización exacta (elimina en destino lo que no esté en origen)
rsync -av --delete origen/ destino/

# Backup remoto vía SSH con compresión
rsync -avz -e "ssh -i ~/.ssh/backup_key" /home/usuario/ user@servidor:/backups/home/

# SSH en puerto no estándar
rsync -avz -e "ssh -p 2222" /datos/ user@host:/backup/datos/

# Dry-run para verificar antes de ejecutar
rsync -avn --delete origen/ destino/

# Excluir múltiples patrones
rsync -av --exclude='*.log' --exclude='*.tmp' --exclude='.git/' origen/ destino/

# Solo ficheros .conf y .sh, excluir todo lo demás
rsync -av --include='*.conf' --include='*.sh' --exclude='*' origen/ destino/

# Backup incremental con directorio por fecha
rsync -av --backup --backup-dir=/backups/$(date +%Y%m%d) --delete /datos/ /backup/actual/

# Preservar ACLs y xattrs (sistemas con soporte extendido)
rsync -aAX origen/ destino/

# Limitar ancho de banda a 1 MB/s
rsync -av --bwlimit=1024 /datos/ user@host:/backup/

# Reanudar transferencia interrumpida
rsync -avP --partial-dir=.rsync-tmp origen/ user@host:destino/

# Solo actualizar ficheros más nuevos en origen
rsync -avtu origen/ destino/

# Transferencia local sin delta encoding (más rápido en discos locales rápidos)
rsync -aW origen/ destino/
```
