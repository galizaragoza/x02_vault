tr (translate) traduce, elimina o comprime caracteres carácter a carácter leyendo de stdin y escribiendo a stdout. A diferencia de `sed` o `awk`, opera a nivel de bytes individuales, no de palabras o líneas. No acepta nombres de fichero como argumento: siempre lee de stdin. Es ideal en pipelines para normalizar texto, eliminar caracteres no deseados, cambiar mayúsculas/minúsculas o limpiar salidas de otros comandos.

```
tr [opciones] CONJUNTO1 [CONJUNTO2]
```

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-d` / `--delete` | Elimina los caracteres de CONJUNTO1 de la entrada. No usa CONJUNTO2. | `tr -d '\n'` |
| `-s` / `--squeeze-repeats` | Comprime secuencias de caracteres repetidos de CONJUNTO1 (o de CONJUNTO2 si se proporcionan ambos) a una sola ocurrencia. | `tr -s ' '` |
| `-c` / `-C` / `--complement` | Usa el complemento de CONJUNTO1 (todos los bytes que NO están en CONJUNTO1). | `tr -cd '[:alnum:]'` |
| `-t` / `--truncate-set1` | Trunca CONJUNTO1 al tamaño de CONJUNTO2 antes de traducir. Evita que CONJUNTO1 más largo cause comportamiento inesperado. | `tr -t '[:upper:]' '[:lower:]'` |
| `--help` | Muestra la ayuda. | `tr --help` |
| `--version` | Muestra la versión. | `tr --version` |

---

## Notación de conjuntos

| Notación | Descripción |
|----------|-------------|
| `a-z` | Rango de caracteres (ASCII). Ej: `a-z`, `0-9`, `A-Z`. |
| `\n` | Newline. |
| `\t` | Tabulación. |
| `\r` | Retorno de carro. |
| `\\` | Barra invertida literal. |
| `\NNN` | Carácter con valor octal NNN. |
| `[CHAR*N]` | Carácter CHAR repetido N veces (para equilibrar longitudes). `[x*]` = `x` tantas veces como sea necesario. | 
| `[::class::]` | Clase de caracteres POSIX (ver tabla abajo). |

**Clases POSIX:**

| Clase | Descripción |
|-------|-------------|
| `[:alpha:]` | Letras (a-z, A-Z). |
| `[:digit:]` | Dígitos (0-9). |
| `[:alnum:]` | Letras y dígitos. |
| `[:upper:]` | Mayúsculas (A-Z). |
| `[:lower:]` | Minúsculas (a-z). |
| `[:space:]` | Espacios en blanco (espacio, tab, newline, etc.). |
| `[:blank:]` | Espacio y tabulación. |
| `[:punct:]` | Signos de puntuación. |
| `[:print:]` | Caracteres imprimibles (incluye espacio). |
| `[:graph:]` | Caracteres imprimibles excepto espacio. |
| `[:cntrl:]` | Caracteres de control. |
| `[:xdigit:]` | Dígitos hexadecimales (0-9, a-f, A-F). |

---

## Casos de uso comunes

```bash
# Convertir a mayúsculas
echo "hola mundo" | tr '[:lower:]' '[:upper:]'

# Convertir a minúsculas
echo "HOLA" | tr '[:upper:]' '[:lower:]'

# Eliminar newlines (unir todo en una línea)
cat fichero.txt | tr -d '\n'

# Eliminar espacios en blanco
echo "texto  con  espacios" | tr -d ' '

# Comprimir múltiples espacios a uno
echo "a   b   c" | tr -s ' '

# Eliminar todo excepto letras y números
echo "he11o w0r1d!" | tr -cd '[:alnum:]'

# Reemplazar espacios por guiones bajos
echo "nombre con espacios" | tr ' ' '_'

# Eliminar caracteres no imprimibles
cat fichero_binario | tr -cd '[:print:]\n'

# Reemplazar newlines por espacios (unir líneas)
cat lista.txt | tr '\n' ' '

# Invertir mayúsculas y minúsculas
echo "HolaMundo" | tr '[:upper:][:lower:]' '[:lower:][:upper:]'

# Eliminar retornos de carro de ficheros Windows (CRLF → LF)
cat fichero_windows.txt | tr -d '\r' > fichero_unix.txt

# Extraer solo dígitos
echo "IP: 192.168.1.100 PORT: 8080" | tr -cd '0-9\n'

# Cifrado ROT13
echo "mensaje secreto" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```
