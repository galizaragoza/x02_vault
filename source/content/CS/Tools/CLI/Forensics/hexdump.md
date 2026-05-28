hexdump muestra el contenido de ficheros binarios en varios formatos de representación: hexadecimal, octal, decimal o ASCII. Es una herramienta estándar para análisis de binarios, inspección de ficheros de firmware, análisis de malware e ingeniería inversa a bajo nivel. Por defecto muestra los datos en grupos de 2 bytes en hexadecimal con el offset al inicio de cada línea. Alternativa común: `xxd` (produce salida más legible con `-C` implícito y permite reconstrucción inversa).

```
hexdump [opciones] fichero [fichero...]
```

---

## Opciones principales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-C` | Formato canónico: offset hex + 16 bytes en hex + representación ASCII. El formato más legible para análisis. | `hexdump -C malware.bin` |
| `-v` / `--no-squeezing` | Muestra todas las líneas sin colapsar líneas repetidas con `*`. Por defecto, las líneas duplicadas se sustituyen por `*`. | `hexdump -Cv shellcode.bin` |
| `-n <N>` | Procesa solo los primeros N bytes. Acepta decimal o hex con prefijo `0x`. | `hexdump -n 64 -C header.bin` |
| `-s <N>` | Salta los primeros N bytes antes de comenzar el dump. Acepta decimal u octal con `0` prefijo o hex con `0x`. | `hexdump -s 0x100 -C firmware.bin` |
| `-e <formato>` | Aplica un formato de salida personalizado (ver sección de formatos). | `hexdump -e '16/1 "%02x " "\n"' file` |
| `-f <archivo>` | Lee el formato desde un fichero (misma sintaxis que `-e`, una cadena por línea). | `hexdump -f formato.fmt binario` |
| `-x` | Muestra datos en hexadecimal de 2 bytes por grupo (formato por defecto sin opciones es similar pero con offset octal). | `hexdump -x shellcode` |
| `-d` | Muestra datos como enteros sin signo de 2 bytes en decimal. | `hexdump -d datos.bin` |
| `-o` | Muestra datos como enteros sin signo de 2 bytes en octal. | `hexdump -o archivo` |
| `-b` | Muestra cada byte individualmente en octal. Útil para ver valores de bytes exactos. | `hexdump -b raw.bin` |
| `-c` | Muestra cada byte como carácter ASCII (caracteres no imprimibles como secuencias de escape). | `hexdump -c texto.bin` |

---

## Formato personalizado con `-e`

La cadena de formato sigue la sintaxis: `'[repeticiones/tamaño] "cadena_printf"'`

- `repeticiones`: cuántas veces aplicar el formato (por defecto: 1).
- `tamaño`: número de bytes a consumir por aplicación (1, 2, 4, 8).
- `cadena_printf`: formato printf estándar; `_p` para char imprimible, `_u` para nombres de control.

| Formato | Descripción | Ejemplo |
|---------|-------------|---------|
| `'1/1 "%02x "'` | Cada byte como hex de 2 dígitos separados por espacio. | `hexdump -e '1/1 "%02x "' file` |
| `'16/1 "%02x " "\n"'` | 16 bytes por línea en hex. | `hexdump -e '16/1 "%02x " "\n"' file` |
| `'4/4 "%08X " "\n"'` | 4 words de 32 bits en hex mayúscula por línea. | `hexdump -e '4/4 "%08X " "\n"' file` |
| `'1/1 "%_p"'` | Cada byte como char imprimible (`.` para no imprimibles). | `hexdump -e '1/1 "%_p"' file` |
| `'"%08.8_ax  " 8/1 "%02x " "  " 8/1 "%02x " "  |" 16/1 "%_p" "|\n"'` | Formato canónico manual similar a `-C`. | Compuesto |

---

## Casos de uso en análisis de seguridad

```bash
# Inspección estándar (canónico, más legible)
hexdump -C malware.bin

# Solo primeros 256 bytes de un ejecutable (magic bytes, headers)
hexdump -C -n 256 binario.elf

# A partir del offset 0x200 (skip de cabecera)
hexdump -C -s 0x200 firmware.bin

# Buscar secuencia de bytes en dump (con grep)
hexdump -C shellcode.bin | grep "90 90 90"

# Mostrar sin colapsar líneas repetidas (importante para ver NOPs sleds completos)
hexdump -Cv -n 512 exploit.bin

# Extraer bytes como stream hex para análisis posterior
hexdump -v -e '1/1 "%02x"' payload.bin

# Alternativa con xxd (más legible, permite reconstrucción inversa)
xxd malware.bin
xxd -r hex_dump.txt > reconstructed.bin

# Comparar dos binarios offset a offset
diff <(hexdump -C original.bin) <(hexdump -C patched.bin)
```
