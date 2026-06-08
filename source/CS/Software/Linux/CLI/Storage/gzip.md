gzip comprime o descomprime ficheros usando el algoritmo DEFLATE (LZ77 + Huffman). Por defecto reemplaza el fichero original por su versión comprimida con extensión `.gz` y preserva timestamps. Es el formato de compresión más extendido en sistemas Unix/Linux; se usa habitualmente junto a `tar` (`.tar.gz` / `.tgz`). Para comprimir múltiples ficheros en un solo archivo, combinar con `tar`.

```
gzip [opciones] [fichero...]
gunzip [opciones] [fichero.gz...]   # equivale a gzip -d
zcat [fichero.gz...]                 # equivale a gzip -dc
```

---

## Operaciones principales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| (sin opciones) | Comprime el fichero y reemplaza el original por `fichero.gz`. | `gzip fichero.txt` |
| `-d` / `--decompress` / `--uncompress` | Descomprime (equivale a `gunzip`). | `gzip -d fichero.txt.gz` |
| `-c` / `--stdout` / `--to-stdout` | Escribe resultado en stdout; no modifica los ficheros de entrada. | `gzip -c fichero.txt > fichero.txt.gz` |
| `-k` / `--keep` | Mantiene el fichero original después de comprimir/descomprimir. | `gzip -k fichero.txt` |
| `-f` / `--force` | Fuerza la operación aunque el destino exista, aunque el fichero tenga múltiples enlaces o aunque la entrada sea un terminal. | `gzip -f fichero.txt` |
| `-r` / `--recursive` | Comprime/descomprime recursivamente todos los ficheros en directorios. | `gzip -r directorio/` |
| `-t` / `--test` | Verifica la integridad del archivo comprimido sin descomprimir. Código de salida 0 si OK. | `gzip -t fichero.txt.gz` |
| `-l` / `--list` | Muestra estadísticas del fichero comprimido: tamaño comprimido/descomprimido, ratio, nombre. | `gzip -l fichero.txt.gz` |

---

## Nivel de compresión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-1` / `--fast` | Compresión más rápida, menor ratio. | `gzip -1 fichero.txt` |
| `-6` | Nivel por defecto (balance velocidad/ratio). | `gzip -6 fichero.txt` |
| `-9` / `--best` | Máxima compresión, más lento. | `gzip -9 fichero.txt` |
| `--fast` | Alias de `-1`. | `gzip --fast *.log` |
| `--best` | Alias de `-9`. | `gzip --best backup.tar` |

---

## Salida y nombre

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v` / `--verbose` | Muestra nombre del fichero y porcentaje de reducción. | `gzip -v fichero.txt` |
| `-q` / `--quiet` | Suprime mensajes de advertencia. | `gzip -q *.log` |
| `-n` / `--no-name` | No guarda ni restaura el nombre original ni el timestamp en el header gzip. | `gzip -n fichero.txt` |
| `-N` / `--name` | Guarda/restaura el nombre original y timestamp del header (comportamiento por defecto). | `gzip -Nd fichero.gz` |
| `-S <sufijo>` / `--suffix=<sufijo>` | Usa un sufijo diferente a `.gz`. | `gzip -S .tgz fichero.tar` |

---

## Casos de uso comunes

```bash
# Comprimir fichero (el original se elimina)
gzip fichero.txt

# Comprimir manteniendo el original
gzip -k fichero.txt

# Descomprimir
gzip -d fichero.txt.gz
gunzip fichero.txt.gz

# Ver contenido sin descomprimir
zcat fichero.txt.gz
zcat fichero.txt.gz | grep "patrón"

# Comprimir con máximo ratio (para archivado largo plazo)
gzip -9 backup.tar

# Comprimir rápido (logs en rotación, prioridad velocidad)
gzip -1 access.log

# Comprimir directorio completo de logs
gzip -r /var/log/nginx/

# Verificar integridad
gzip -t fichero.gz && echo "OK"

# Ver estadísticas de compresión
gzip -l *.gz

# Comprimir y mantener: pipeline sin intermedios
cat fichero.txt | gzip > fichero.txt.gz

# Con tar (lo más habitual)
tar -czf backup.tar.gz /datos/
tar -xzf backup.tar.gz
```
