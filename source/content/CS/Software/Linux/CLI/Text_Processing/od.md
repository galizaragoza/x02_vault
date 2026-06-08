od (octal dump) vuelca el contenido de un fichero o de stdin en distintas representaciones: octal (por defecto), hexadecimal, decimal, ASCII o coma flotante. Permite inspeccionar byte a byte datos binarios, cabeceras, caracteres no imprimibles y alineamiento de estructuras. Es complementario de `strings` (texto imprimible) y `hexdump`/`xxd`, con la ventaja de su control fino sobre el radix de offsets y el tamaño de las unidades.

```
od [opciones] [fichero...]
od --traditional [fichero] [[+]offset[.][b] [[+]label[.][b]]]
```

Sin `-t`, el formato por defecto equivale a `-t o2` (unidades octales de 2 bytes) con offsets octales.

---

## Selección de formato de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t <tipo>` / `--format=<tipo>` | Selecciona uno o varios formatos de salida (ver tabla de tipos). | `od -t x1 binario` |
| `-A <radix>` / `--address-radix=<radix>` | Radix del offset (columna izquierda): `d` decimal, `o` octal, `x` hex, `n` ninguno. | `od -A x -t x1 fichero` |
| `-w[N]` / `--width[=N]` | N bytes por línea de salida (por defecto 16, o 32 según formato). | `od -w8 -t x1 fichero` |
| `-v` / `--output-duplicates` | No comprime líneas idénticas con `*`; las imprime todas. | `od -v -t x1 ceros.bin` |

---

## Tipos para `-t` (`--format`)

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| `a` | Carácter con nombre, ignorando el bit de mayor peso (`nul`, `sp`, `nl`...). | `od -t a fichero` |
| `c` | Carácter ASCII imprimible o secuencia de escape con barra invertida (`\n`, `\t`, `\0`). | `od -c fichero` |
| `d[N]` | Decimal con signo, N bytes por entero. | `od -t d4 fichero` |
| `u[N]` | Decimal sin signo, N bytes por entero. | `od -t u2 fichero` |
| `o[N]` | Octal, N bytes por entero. | `od -t o1 fichero` |
| `x[N]` | Hexadecimal, N bytes por entero. | `od -t x1 fichero` |
| `f[N]` | Coma flotante, N bytes (`F`=float, `D`=double, `L`=long double). | `od -t fF datos.bin` |
| sufijo de tamaño con letra | En enteros: `C`=char, `S`=short, `I`=int, `L`=long en vez de un número de bytes. | `od -t dI fichero` |
| sufijo `z` | Añade al final de cada línea los caracteres imprimibles (estilo `hexdump -C`). | `od -A x -t x1z fichero` |

Se pueden encadenar varios formatos: `od -A x -t x1z -t c fichero` muestra hex + ASCII y, debajo, la interpretación como caracteres.

---

## Recorte y posicionamiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-j <bytes>` / `--skip-bytes=<bytes>` | Salta los primeros `<bytes>` bytes de la entrada antes de volcar. | `od -A d -j 512 -t x1 disco.img` |
| `-N <bytes>` / `--read-bytes=<bytes>` | Limita el volcado a `<bytes>` bytes leídos. | `od -N 64 -t x1 cabecera.bin` |
| `-S [bytes]` / `--strings[=bytes]` | Imprime solo secuencias de al menos `bytes` caracteres gráficos (por defecto 3). | `od -S 6 binario` |

Los valores de bytes admiten sufijos de multiplicador: `b`=512, `KB`=1000, `K`=1024, `MB`=1000², `M`=1024², `GB`, `G`, etc.

---

## Atajos de formato heredados (estilo BSD)

Equivalen a un `-t` concreto; se mantienen por compatibilidad.

| Opción | Equivalente | Descripción |
|--------|-------------|-------------|
| `-a` | `-t a` | Caracteres con nombre, ignorando bit alto. |
| `-b` | `-t o1` | Bytes en octal. |
| `-c` | `-t c` | Caracteres ASCII o escapes con barra invertida. |
| `-d` | `-t u2` | Unidades decimales sin signo de 2 bytes. |
| `-f` | `-t fF` | Floats. |
| `-i` | `-t dI` | Enteros decimales con signo. |
| `-l` | `-t dL` | Longs decimales con signo. |
| `-o` | `-t o2` | Unidades octales de 2 bytes (por defecto). |
| `-s` | `-t d2` | Unidades decimales con signo de 2 bytes. |
| `-x` | `-t x2` | Unidades hexadecimales de 2 bytes. |

---

## Modo tradicional y misceláneos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--traditional` | Acepta la sintaxis heredada con `offset` y `label` posicionales. | `od --traditional -A x fichero +256.` |
| `--help` | Muestra ayuda y lista de opciones. | `od --help` |
| `--version` | Muestra la versión. | `od --version` |

---

## Casos de uso comunes

```bash
# Volcado hex + ASCII al estilo hexdump -C
od -A x -t x1z fichero.bin

# Ver los caracteres reales (incluidos \n, \t, \0)
od -c fichero.txt

# Inspeccionar los primeros 16 bytes (número mágico de un formato)
od -A x -t x1 -N 16 imagen.png

# Saltar una cabecera de 512 bytes y leer los siguientes 64
od -A d -j 512 -N 64 -t x1 disco.img

# Detectar bytes nulos o no imprimibles en un fichero "de texto"
od -c sospechoso.txt | grep -E '\\0|\\r'

# Convertir un volcado de enteros de 4 bytes a decimal
od -A n -t d4 datos.bin

# Mostrar todas las líneas sin comprimir repeticiones con *
od -v -A x -t x1 ceros.bin

# Ver el endianness de un campo (comparar -t x4 vs bytes sueltos)
od -A x -t x4 -N 8 estructura.bin
```
