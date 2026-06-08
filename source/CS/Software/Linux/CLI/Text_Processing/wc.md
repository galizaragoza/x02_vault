wc (word count) cuenta líneas, palabras, bytes y caracteres en ficheros o stdin. Sin opciones, imprime las tres métricas principales: número de newlines, palabras y bytes, en ese orden. Con múltiples ficheros, añade una línea `total` al final. Es una herramienta esencial en pipelines para medir el volumen de datos o la longitud de salidas.

```
wc [opciones] [fichero...]
```

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` / `--lines` | Cuenta el número de newlines (líneas). | `wc -l fichero.txt` |
| `-w` / `--words` | Cuenta el número de palabras (secuencias de caracteres no-espacio separadas por blancos). | `wc -w documento.txt` |
| `-c` / `--bytes` | Cuenta el número de bytes. | `wc -c binario.bin` |
| `-m` / `--chars` | Cuenta el número de caracteres (sensible al locale/UTF-8; difiere de `-c` con multibyte). | `wc -m texto_utf8.txt` |
| `-L` / `--max-line-length` | Imprime la longitud de la línea más larga (en caracteres de pantalla). | `wc -L fichero.txt` |
| `--files0-from=<fichero>` | Lee la lista de nombres de fichero desde `<fichero>`, terminados en NUL (compatible con `find -print0`). Usar `-` para stdin. | `find . -name '*.c' -print0 \| wc -l --files0-from=-` |

---

## Casos de uso comunes

```bash
# Contar líneas de un fichero
wc -l /etc/passwd

# Contar palabras en un documento
wc -w documento.txt

# Contar tamaño en bytes
wc -c imagen.jpg

# Todo a la vez (newlines, palabras, bytes)
wc fichero.txt

# Contar líneas de salida de un comando
grep "ERROR" app.log | wc -l

# Contar número de ficheros en un directorio
ls | wc -l

# Línea más larga en un fichero (para detectar líneas muy largas en código)
wc -L *.c

# Contar caracteres (no bytes) en texto UTF-8
wc -m texto_utf8.txt

# Procesar lista de ficheros con NUL separador
find /src -name '*.py' -print0 | wc -l --files0-from=-
```
