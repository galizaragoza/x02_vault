file determina el tipo de un fichero examinando su contenido, no su extensión. Aplica tres baterías de pruebas en orden —pruebas del sistema de ficheros (`stat`), pruebas de número mágico (patrones de bytes definidos en la base de datos *magic*) y pruebas de lenguaje/texto (codificación y tokens)— y devuelve la primera que tenga éxito. Es la herramienta de referencia para identificar binarios, cabeceras, ficheros sin extensión y artefactos en análisis forense.

```
file [opciones] fichero [fichero...]
```

---

## Identificación y formato de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-b` / `--brief` | No antepone el nombre del fichero a la salida. | `file -b /bin/ls` |
| `-i` / `--mime` | Salida en formato MIME (tipo y codificación). | `file -i documento.pdf` |
| `--mime-type` | Solo el tipo MIME (`text/plain`, `application/pdf`...). | `file --mime-type script.sh` |
| `--mime-encoding` | Solo la codificación MIME (`us-ascii`, `utf-8`, `binary`). | `file --mime-encoding nota.txt` |
| `--apple` | Imprime el código CREATOR/TYPE de Apple (4+4 caracteres). | `file --apple imagen.png` |
| `--extension` | Lista las extensiones válidas para el tipo detectado, separadas por `/`. | `file --extension foto.jpg` |
| `-F <sep>` / `--separator <sep>` | Usa `<sep>` en vez de `:` entre nombre y tipo. | `file -F ' => ' *.bin` |
| `-N` / `--no-pad` | No alinea (rellena) la salida para múltiples ficheros. | `file -N *` |
| `-n` / `--no-buffer` | Vacía la salida tras cada fichero (útil en pipelines/loops largos). | `file -n -f lista.txt` |
| `-r` / `--raw` | No traduce caracteres no imprimibles a `\ooo` octal. | `file -r binario` |
| `-0` / `--print0` | Termina el nombre del fichero con NUL en vez de `:` (combinar con `-f` o `xargs -0`). | `file -0 * \| xargs -0 ...` |

---

## Entrada de ficheros

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-f <fichero>` / `--files-from <fichero>` | Lee la lista de nombres a analizar de `<fichero>` (uno por línea); `-` para stdin. | `find . -type f \| file -f -` |
| `-s` / `--special-files` | Lee también dispositivos de bloque/carácter (normalmente omitidos). | `file -s /dev/sda1` |
| `-L` / `--dereference` | Sigue los enlaces simbólicos (analiza el destino). | `file -L /usr/bin/vi` |
| `-h` / `--no-dereference` | No sigue enlaces simbólicos (comportamiento por defecto). | `file -h enlace` |
| `-p` / `--preserve-date` | Conserva el atime del fichero (evita marcar el acceso). | `file -p evidencia.bin` |

---

## Lógica de detección y pruebas

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-k` / `--keep-going` | No se detiene en la primera coincidencia; muestra todas las que apliquen (separadas por `\012-`). | `file -k firmware.bin` |
| `-e <test>` / `--exclude <test>` | Excluye una prueba de la lista de detección. | `file -e elf -e text muestra` |
| `--exclude-quiet` | Como `-e` pero ignora silenciosamente nombres de prueba desconocidos. | `file --exclude-quiet=foo x` |
| `-z` / `--uncompress` | Mira dentro de ficheros comprimidos (gzip, bzip2, xz...). | `file -z backup.tar.gz` |
| `-Z` / `--uncompress-noreport` | Solo informa del contenido interno, no del envoltorio comprimido. | `file -Z backup.tar.gz` |

**Nombres válidos para `-e <test>`:** `apptype`, `ascii`, `cdf`, `compress`, `csv`, `elf`, `encoding`, `json`, `soft`, `tar`, `text`, `tokens`.

---

## Base de datos *magic* y depuración

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-m <lista>` / `--magic-file <lista>` | Usa los ficheros magic indicados (separados por `:`) en vez del por defecto. | `file -m /etc/magic:./custom.magic x` |
| `-C` / `--compile` | Compila la base magic a `<fichero>.mgc` (optimiza el arranque). | `file -C -m custom.magic` |
| `-c` / `--checking-printout` | Vuelca la versión analizada del magic (depurar reglas, usar con `-m`). | `file -c -m custom.magic` |
| `-d` | Imprime el magic interno por defecto (depuración). | `file -d` |
| `-l` / `--list` | Lista los patrones magic y su "fuerza" (orden de prioridad). | `file -l` |
| `-P <nombre=valor>` / `--parameter <nombre=valor>` | Ajusta límites internos del motor de detección. | `file -P bytes=8192 binario` |
| `-S` / `--no-sandbox` | Desactiva el sandbox de llamadas al sistema (seccomp). | `file -S /dev/mem` |
| `-v` / `--version` | Versión de `file` y de la base magic. | `file -v` |
| `--help` | Ayuda y lista de opciones. | `file --help` |

**Parámetros válidos para `-P`:** `bytes` (máx. bytes leídos), `elf_notes`, `elf_phnum`, `elf_shnum` (límites de cabeceras ELF), `encoding`, `indir` (nivel máx. de indirección), `name` (recursión de nombres), `regex` (longitud máx. de regex magic).

---

## Casos de uso comunes

```bash
# Identificar un binario sin extensión
file binario_misterioso

# Tipo MIME para servir o validar uploads
file --mime-type subida.dat

# Inspeccionar una partición o dispositivo (requiere -s)
file -s /dev/sda1

# Analizar en masa el resultado de find, seguro ante nombres raros
find . -type f -print0 | xargs -0 file

# Detectar el sistema de ficheros de una imagen de disco
file -s disco.img

# Mirar dentro de un comprimido sin descomprimirlo a disco
file -z captura.pcap.gz

# Filtrar solo ejecutables ELF en un directorio
file * | grep ELF

# Ver todas las coincidencias posibles (forense)
file -k artefacto.bin

# Comprobar codificación de un fichero de texto
file -i documento.txt
```
