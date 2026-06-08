cp copia ficheros y directorios. Sin opciones copia un fichero individual; para directorios requiere `-r`. Puede preservar metadatos, crear hard links o symlinks, hacer backups automáticos de los destinos y actualizar solo ficheros más antiguos. Es la herramienta estándar para copias locales en sistemas Unix/Linux.

```
cp [opciones] origen destino
cp [opciones] origen... directorio
```

---

## Opciones principales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r` / `-R` / `--recursive` | Copia directorios recursivamente, incluyendo su contenido. | `cp -r dir1/ dir2/` |
| `-a` / `--archive` | Modo archivo: equivale a `-dR --preserve=all`. Preserva todos los atributos y estructura. | `cp -a origen/ backup/` |
| `-p` / `--preserve[=atribs]` | Preserva atributos especificados: `mode`, `ownership`, `timestamps`, `links`, `context`, `xattr`, `all`. Sin argumento preserva modo, propietario y timestamps. | `cp -p fichero backup/` |
| `-d` / `--no-dereference` | Preserva symlinks en lugar de copiar los ficheros apuntados. | `cp -d symlink destino/` |
| `-L` / `--dereference` | Copia los ficheros referenciados por symlinks (los dereferencia). Por defecto con ficheros regulares. | `cp -L link destino/` |
| `-l` / `--link` | Crea hard links en lugar de copiar. | `cp -l fichero hardlink` |
| `-s` / `--symbolic-link` | Crea symlinks en lugar de copiar. | `cp -s /ruta/fichero symlink` |

---

## Control de sobreescritura y actualización

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` / `--interactive` | Pregunta antes de sobreescribir. | `cp -i fichero destino/` |
| `-n` / `--no-clobber` | No sobreescribe ficheros existentes. | `cp -n fichero destino/` |
| `-f` / `--force` | Fuerza la copia aunque el destino no sea escribible (elimina y vuelve a intentar). | `cp -f fichero destino/` |
| `-u` / `--update` | Solo copia si el origen es más nuevo que el destino, o si el destino no existe. | `cp -u fichero destino/` |
| `--update=older` | Copia si el origen es más nuevo (comportamiento por defecto de `-u`). | `cp --update=older fichero destino/` |

---

## Backups

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-b` | Crea backup del fichero destino antes de sobreescribirlo. Usa el sufijo definido por `--suffix` o `~`. | `cp -b fichero destino/` |
| `--backup[=CONTROL]` | Como `-b` pero con control del método: `none`, `off`, `numbered`/`t`, `existing`/`nil`, `simple`/`never`. | `cp --backup=numbered fichero destino/` |
| `-S <sufijo>` / `--suffix=<sufijo>` | Define el sufijo de backup. Por defecto: `~`. | `cp -b -S .bak fichero destino/` |

---

## Destino y comportamiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t <dir>` / `--target-directory=<dir>` | Copia todos los orígenes al directorio indicado (evita ambigüedad con múltiples orígenes). | `cp -t /destino/ *.txt` |
| `-T` / `--no-target-directory` | Trata el destino como fichero, no como directorio. | `cp -T origen destino` |
| `-v` / `--verbose` | Muestra cada fichero copiado. | `cp -rv dir1/ dir2/` |
| `--parents` | Preserva la estructura de directorios del origen en el destino. | `cp --parents src/lib/file.c /dest/` |
| `-x` / `--one-file-system` | No cruza límites de sistemas de ficheros. | `cp -rx / /mnt/backup/` |
| `--sparse=CUANDO` | Controla creación de sparse files: `auto`, `always`, `never`. | `cp --sparse=always imagen.raw /dest/` |
| `--reflink[=CUANDO]` | Usa CoW (copy-on-write) si el sistema de ficheros lo soporta (btrfs, XFS): `auto`, `always`, `never`. | `cp --reflink=auto fichero destino/` |

---

## Casos de uso comunes

```bash
# Copia simple
cp fichero.txt /destino/

# Copia recursiva de directorio
cp -r proyecto/ /backup/proyecto/

# Modo archivo (preserva todo, para backups)
cp -a /home/usuario/ /backup/usuario/

# Solo actualizar ficheros más nuevos
cp -u *.conf /etc/app/

# Sin sobreescribir existentes
cp -n *.jpg /fotos/

# Con backup automático del destino
cp -b --suffix=.bak configuracion.conf /etc/

# Preservar permisos y timestamps
cp -p fichero.sh /usr/local/bin/

# Múltiples ficheros a directorio
cp -t /tmp/ file1.txt file2.txt file3.sh

# Estructura de directorios preservada
cp --parents src/module/file.c /build/
```
