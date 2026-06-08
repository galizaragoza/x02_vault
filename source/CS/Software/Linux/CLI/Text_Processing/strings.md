strings extrae secuencias de caracteres imprimibles de ficheros binarios, incluyendo ejecutables, bibliotecas, volcados de memoria y firmware. Se usa principalmente en análisis de malware, ingeniería inversa y forense digital para localizar credenciales embebidas, URLs, rutas de fichero, mensajes de error y otros artefactos de cadenas de texto en binarios. La longitud mínima por defecto es 4 caracteres.

```
strings [opciones] fichero [fichero...]
```

---

## Opciones principales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` / `--all` | Escanea todo el fichero, no solo las secciones de datos inicializadas. Necesario para ELF sin tabla de secciones. | `strings -a firmware.bin` |
| `-d` / `--data` | Imprime solo strings de las secciones de datos del objeto (sección `.data`, `.rodata`, etc.). Opuesto de `-a`. | `strings -d malware.elf` |
| `-f` / `--print-file-name` | Precede cada string con el nombre del fichero. Útil al procesar múltiples binarios. | `strings -f *.so` |
| `-n <n>` / `--bytes=<n>` / `-<n>` | Longitud mínima de la secuencia de caracteres para ser impresa. Por defecto: 4. | `strings -n 8 binary.exe` |
| `-o` | Muestra el offset de cada string en formato octal (equivale a `-t o`). | `strings -o executable` |
| `-t <formato>` / `--radix=<formato>` | Muestra el offset de cada string. Formatos: `d` (decimal), `o` (octal), `x` (hexadecimal). | `strings -t x shellcode.bin` |
| `-e <codif>` / `--encoding=<codif>` | Especifica la codificación de caracteres a buscar. | `strings -e l malware.dll` |
| `-T <tipo>` / `--target=<tipo>` | Especifica el formato del objeto BFD (binutils). | `strings -T pe-i386 binary.exe` |
| `-w` / `--include-all-whitespace` | Incluye caracteres de espacio en blanco (espacio, tabulación) como caracteres imprimibles. | `strings -w binary` |
| `-s <sep>` / `--output-separator=<sep>` | Define el separador entre strings en la salida. | `strings -s $'\n---\n' binary` |
| `--unicode=<modo>` | Controla cómo se manejan strings UTF-16: `default`, `locale`, `invalid`, `hex`, `highlight`, `escape`. | `strings --unicode=highlight binary` |
| `--help` | Muestra ayuda y lista de opciones. | `strings --help` |
| `--version` | Muestra la versión. | `strings --version` |

---

## Valores del parámetro `-e` (encoding)

| Valor | Descripción |
|-------|-------------|
| `s` | Caracteres de un solo byte (por defecto, 7-bit ASCII). |
| `S` | Caracteres de un solo byte (8-bit). |
| `b` | UTF-16 big-endian (2 bytes por carácter). |
| `l` | UTF-16 little-endian (2 bytes por carácter). Común en PE/DLL de Windows. |
| `B` | UTF-32 big-endian (4 bytes por carácter). |
| `L` | UTF-32 little-endian (4 bytes por carácter). |

---

## Casos de uso en análisis de seguridad

```bash
# Búsqueda básica en binario
strings malware.bin

# Mostrar con offset hexadecimal (útil para correlacionar en IDA/Ghidra)
strings -t x -a sample.exe

# Buscar strings largas (reduce falsos positivos)
strings -n 10 firmware.bin

# Buscar strings UTF-16 (binarios PE de Windows)
strings -e l malware.dll

# Combinado: UTF-8 y UTF-16 little-endian en un binario Windows
strings -a malware.exe && strings -a -e l malware.exe

# Buscar URLs, IPs o dominios en un binario
strings -a malware.bin | grep -E '(https?://|[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3})'

# Buscar posibles credenciales
strings binary | grep -iE '(password|passwd|secret|key|token|api)'

# Procesar múltiples ficheros con nombre de origen
strings -f /lib/*.so | grep "interesting"

# Extraer strings de volcado de memoria (formato crudo)
strings -a -n 6 memory.dmp | sort -u
```
