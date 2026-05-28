tar (tape archive) es una utilidad para crear, modificar y extraer archivos de archivo que agrupan múltiples ficheros y directorios en uno solo. Soporta compresión mediante gzip, bzip2, xz, zstd y otros filtros, y preserva metadatos como permisos, propietario y timestamps. Es la herramienta estándar en sistemas Unix/Linux para empaquetado y distribución de software.

```
tar [OPERACIÓN] [OPCIONES] [ARCHIVO] [FICHEROS...]
```

---

## Operaciones principales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-c` / `--create` | Crea un nuevo archivo tar. | `tar -cf archivo.tar /ruta/` |
| `-x` / `--extract` | Extrae contenido del archivo. | `tar -xf archivo.tar` |
| `-t` / `--list` | Lista el contenido del archivo sin extraer. | `tar -tf archivo.tar` |
| `-r` / `--append` | Añade ficheros al final de un archivo existente (solo tar sin comprimir). | `tar -rf archivo.tar nuevo.txt` |
| `-u` / `--update` | Añade solo ficheros más nuevos que los existentes en el archivo. | `tar -uf archivo.tar dir/` |
| `-d` / `--diff` / `--compare` | Compara el contenido del archivo con el sistema de ficheros. | `tar -df archivo.tar` |
| `--delete` | Elimina ficheros del archivo (solo tar sin comprimir). | `tar --delete -f archivo.tar fichero.txt` |
| `-A` / `--concatenate` | Concatena dos archivos tar. | `tar -Af base.tar nuevo.tar` |

---

## Opciones obligatorias y de archivo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-f <archivo>` / `--file=<archivo>` | Especifica el archivo tar a usar. Usar `-` para stdin/stdout. | `tar -cf backup.tar /home/` |
| `-` | Usa stdin/stdout como fuente/destino. | `tar -cf - /dir \| ssh host tar -xf -` |

---

## Compresión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-z` / `--gzip` | Comprime o descomprime con gzip (.tar.gz / .tgz). | `tar -czf backup.tar.gz /home/` |
| `-j` / `--bzip2` | Comprime o descomprime con bzip2 (.tar.bz2). | `tar -cjf backup.tar.bz2 /home/` |
| `-J` / `--xz` | Comprime o descomprime con xz (.tar.xz). | `tar -cJf backup.tar.xz /home/` |
| `--zstd` | Comprime o descomprime con zstd (.tar.zst). | `tar --zstd -cf backup.tar.zst /home/` |
| `-Z` / `--compress` | Comprime con compress antiguo (.tar.Z). | `tar -cZf backup.tar.Z /home/` |
| `--lzip` | Comprime con lzip. | `tar --lzip -cf backup.tar.lz /home/` |
| `--lzma` | Comprime con lzma. | `tar --lzma -cf backup.tar.lzma /home/` |
| `-I <prog>` / `--use-compress-program=<prog>` | Usa un programa de compresión externo. | `tar -I 'zstd -9' -cf backup.tar.zst /home/` |

---

## Salida y verbosidad

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v` / `--verbose` | Muestra los ficheros procesados. | `tar -cvf backup.tar /home/` |
| `-vv` | Verbose doble: muestra permisos y metadatos adicionales. | `tar -cvvf backup.tar /home/` |
| `--checkpoint[=N]` | Muestra un punto de control cada N registros (por defecto: 10). | `tar --checkpoint=100 -cf backup.tar /data/` |
| `--checkpoint-action=<acción>` | Acción al llegar a un checkpoint (`echo`, `dot`, `ttyout`, exec). | `tar --checkpoint --checkpoint-action=dot -cf backup.tar /data/` |
| `--totals` | Muestra el total de bytes escritos al finalizar. | `tar --totals -czf backup.tar.gz /home/` |
| `-p` / `--show-progress` | Muestra progreso (versiones recientes). | `tar --show-progress -czf backup.tar.gz /home/` |

---

## Control de permisos y metadatos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-p` / `--preserve-permissions` | Preserva los permisos originales al extraer (requiere root para permisos especiales). | `tar -xpf backup.tar` |
| `--no-same-permissions` | Aplica umask al extraer (comportamiento por defecto para usuarios normales). | `tar -xf backup.tar --no-same-permissions` |
| `--same-owner` | Preserva propietario y grupo al extraer (requiere root). | `tar -xf backup.tar --same-owner` |
| `--no-same-owner` | No preserva propietario al extraer. | `tar -xf backup.tar --no-same-owner` |
| `--preserve` | Equivale a `-p --same-owner`. | `tar -xf backup.tar --preserve` |
| `--numeric-owner` | Usa UID/GID numéricos en lugar de nombres. | `tar -cf backup.tar --numeric-owner /home/` |
| `--atime-preserve` | Preserva el atime de los ficheros accedidos. | `tar --atime-preserve -cf backup.tar /home/` |
| `--mtime=<fecha>` | Establece el mtime de los ficheros archivados. | `tar -cf backup.tar --mtime='2024-01-01' /home/` |

---

## Exclusión e inclusión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--exclude=<patrón>` | Excluye ficheros que coincidan con el patrón glob. | `tar -czf backup.tar.gz /home/ --exclude='*.log'` |
| `--exclude-from=<archivo>` | Lee patrones de exclusión desde un archivo. | `tar -czf backup.tar.gz /home/ --exclude-from=.tarignore` |
| `--exclude-vcs` | Excluye directorios de control de versiones (.git, .svn, .cvs, etc.). | `tar -czf backup.tar.gz /proyecto/ --exclude-vcs` |
| `--exclude-vcs-ignores` | Lee patrones de `.gitignore` y similares para excluir. | `tar -czf backup.tar.gz /proyecto/ --exclude-vcs-ignores` |
| `--exclude-caches` | Excluye directorios con `CACHEDIR.TAG`. | `tar -czf backup.tar.gz /home/ --exclude-caches` |
| `--exclude-backups` | Excluye ficheros de backup (terminados en `~`, `#...#`, `.#...`). | `tar -czf backup.tar.gz /home/ --exclude-backups` |
| `-X <archivo>` / `--exclude-from=<archivo>` | Lista de patrones de exclusión. | `tar -czf backup.tar.gz /dir/ -X excludes.txt` |
| `--include=<patrón>` | Incluye solo ficheros que coincidan (requiere combinación con exclusiones). | `tar -czf logs.tar.gz /var/log/ --include='*.log' --exclude='*'` |
| `-T <archivo>` / `--files-from=<archivo>` | Lee la lista de ficheros a archivar desde un archivo. | `find /home -mtime -1 -print0 \| tar -czf changes.tar.gz --null -T -` |

---

## Control de directorios

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-C <dir>` / `--directory=<dir>` | Cambia al directorio indicado antes de operar. | `tar -xf backup.tar -C /tmp/restore/` |
| `--strip-components=<n>` | Elimina los primeros `n` componentes del path al extraer. | `tar -xf backup.tar --strip-components=2` |
| `--transform=<expr>` / `--xform=<expr>` | Transforma nombres de ficheros con expresión sed. | `tar -xf backup.tar --transform='s/^usr/opt/'` |
| `-h` / `--dereference` | Archiva el fichero referenciado por enlaces simbólicos, no el enlace. | `tar -czf backup.tar.gz -h /dir/` |
| `--hard-dereference` | Archiva ficheros de hard links en lugar del hard link. | `tar -czf backup.tar.gz --hard-dereference /dir/` |
| `--one-file-system` | No cruza límites de sistemas de ficheros. | `tar -czf backup.tar.gz --one-file-system /` |
| `--no-recursion` | No desciende recursivamente en subdirectorios. | `tar -cf backup.tar --no-recursion /dir/` |
| `--recursion` | Desciende recursivamente (por defecto). | `tar -cf backup.tar --recursion /dir/` |

---

## Opciones de extracción

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-k` / `--keep-old-files` | No sobreescribe ficheros existentes al extraer. | `tar -xkf backup.tar` |
| `--keep-newer-files` | No sobreescribe si el fichero existente es más nuevo. | `tar -xf backup.tar --keep-newer-files` |
| `--overwrite` | Sobreescribe ficheros existentes (por defecto). | `tar -xf backup.tar --overwrite` |
| `--unlink-first` | Elimina el fichero existente antes de extraer. | `tar -xf backup.tar --unlink-first` |
| `-O` / `--to-stdout` | Extrae los ficheros a stdout en lugar del disco. | `tar -xOf backup.tar fichero.txt` |
| `--to-command=<cmd>` | Pasa cada fichero extraído como stdin al comando. | `tar -xf backup.tar --to-command='cat > /dev/null'` |

---

## Backups incrementales (multi-volumen y snapshots)

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-g <archivo>` / `--listed-incremental=<archivo>` | Backup incremental usando snapshot file. Primer uso crea backup completo. | `tar -czg snapshot.snar -f backup.tar.gz /home/` |
| `-G` / `--incremental` | Modo incremental antiguo (sin snapshot file). | `tar -cGf backup.tar /home/` |
| `-M` / `--multi-volume` | Crea o extrae archives multi-volumen. | `tar -cMf /dev/tape /home/` |
| `-L <n>` / `--tape-length=<n>` | Cambia de volumen cada `n` × 1024 bytes. | `tar -cMLf 1440 /dev/fd0 /home/` |
| `-F <cmd>` / `--info-script=<cmd>` | Ejecuta el comando al cambiar de volumen. | `tar -cML 1440 -F /usr/local/bin/tape-change.sh -f /dev/fd0 /home/` |

---

## Casos de uso comunes

```bash
# Crear backup comprimido con xz
tar -cJf backup.tar.xz /home/usuario/

# Extraer en directorio específico
tar -xzf backup.tar.gz -C /tmp/restore/

# Listar contenido sin extraer
tar -tzf backup.tar.gz

# Crear backup excluyendo .git y node_modules
tar -czf proyecto.tar.gz /proyecto/ --exclude-vcs --exclude='node_modules'

# Backup incremental (primera vez crea completo, siguientes solo cambios)
tar -czg /tmp/snapshot.snar -f backup_$(date +%Y%m%d).tar.gz /datos/

# Extraer solo un fichero específico del archivo
tar -xzf backup.tar.gz -C /tmp/ ruta/dentro/del/tar/fichero.txt

# Enviar backup por SSH sin archivo intermedio
tar -czf - /home/ | ssh usuario@servidor "cat > /backups/home.tar.gz"
```
