head imprime las primeras N líneas o N bytes de uno o más ficheros en stdout. Sin opciones, muestra las 10 primeras líneas. Con múltiples ficheros, precede cada bloque con una cabecera `==> nombre <==`. Es fundamental en pipelines para previsualizar ficheros grandes sin cargarlos completos en memoria.

```
head [opciones] [fichero...]
```

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n <N>` / `--lines=<N>` | Muestra las primeras N líneas. Si N tiene prefijo `-`, muestra todas excepto las últimas N líneas. | `head -n 20 file.txt` / `head -n -5 file.txt` |
| `-c <N>` / `--bytes=<N>` | Muestra los primeros N bytes. Acepta sufijos: `K` (1024), `M`, `G`. Si N tiene prefijo `-`, muestra todos excepto los últimos N bytes. | `head -c 512 file.bin` / `head -c -1K file.txt` |
| `-q` / `--quiet` / `--silent` | No imprime cabeceras con nombres de fichero al procesar múltiples ficheros. | `head -q -n 5 *.log` |
| `-v` / `--verbose` | Siempre imprime la cabecera con nombre de fichero, incluso con un solo fichero. | `head -v -n 5 file.txt` |
| `-z` / `--zero-terminated` | El delimitador de línea es NUL en lugar de newline (útil con `find -print0`). | `head -zn 3 file` |

---

## Casos de uso comunes

```bash
# Primeras 10 líneas (por defecto)
head archivo.log

# Primeras 20 líneas
head -n 20 archivo.log

# Todas las líneas excepto la última (útil para eliminar trailing)
head -n -1 archivo.csv

# Primeros 256 bytes de un binario
head -c 256 firmware.bin | hexdump -C

# Previsualizar varios ficheros con cabeceras
head -n 5 *.conf

# Sin cabeceras al previsualizar varios
head -q -n 3 /etc/*.conf

# En pipeline: ver primeras líneas de salida de un comando
dmesg | head -n 30
```
