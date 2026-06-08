awk es un lenguaje de procesamiento de texto orientado a registros y campos: divide cada línea (registro) en campos y ejecuta acciones sobre los que coinciden con un patrón. Un programa awk es una secuencia de reglas `patrón { acción }`. Sobresale donde `cut` se queda corto (reordenar columnas, aritmética, agregaciones, condicionales) sin llegar a la complejidad de un script completo. La implementación habitual en Linux es GNU awk (`gawk`).

```
awk [opciones] 'patrón { acción }' [archivo...]
awk [opciones] -f programa.awk [archivo...]
```

---

## Opciones de línea de comandos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-F <sep>` / `--field-separator <sep>` | Define el separador de campos de entrada (`FS`). Acepta regex. | `awk -F':' '{print $1}' /etc/passwd` |
| `-v <var>=<valor>` / `--assign` | Asigna una variable antes de ejecutar (disponible ya en `BEGIN`). | `awk -v umbral=100 '$3>umbral' datos` |
| `-f <archivo>` / `--file <archivo>` | Lee el programa awk desde un archivo en lugar de la línea de comandos. | `awk -f script.awk datos.txt` |
| `--` | Marca el fin de opciones (lo siguiente son ficheros). | `awk -- '{print}' -fichero-raro` |
| `-V` / `--version` | Muestra la versión. | `awk -V` |

---

## Estructura de una regla y patrones

| Flag/Patrón | Función | Ejemplo de sintaxis |
|-------------|---------|---------------------|
| `BEGIN {}` | Ejecuta antes de procesar la entrada. | `awk 'BEGIN {FS=":"} {print $1}'` |
| `END {}` | Ejecuta tras procesar toda la entrada. | `awk '{sum+=$1} END {print sum}' nums.txt` |
| `/patrón/` | Filtra líneas que coinciden con la regex. | `awk '/FLAGPART:/ {print $NF}' log.txt` |
| `expr` | Patrón booleano: la acción (por defecto `{print}`) se ejecuta si es verdadero. | `awk '$3 > 1000' datos.txt` |
| `patrón1,patrón2` | Rango: desde la línea que casa patrón1 hasta la que casa patrón2. | `awk '/START/,/END/' file` |
| `NR==n` | Procesa solo el registro (línea) n. | `awk 'NR==10 {print}' log.txt` |
| `{print $n}` | Acción: imprime el campo n. | `awk '{print $2}' archivo.txt` |
| `!seen[$0]++` | Elimina duplicados preservando el orden original. | `awk '!seen[$0]++' archivo.txt` |

---

## Variables predefinidas

| Variable | Descripción | Valor por defecto |
|----------|-------------|-------------------|
| `$0` | Toda la línea actual. | Línea completa de entrada |
| `$1, $2, … $n` | Campo n-ésimo de la línea. | Campos individuales |
| `NR` | Número de registro acumulado (línea global). | Incremental |
| `NF` | Número de campos del registro actual. | Depende de la línea |
| `FNR` | Número de registro dentro del archivo actual. | Reinicia por fichero |
| `FS` | Separador de campos de entrada. | Espacio/tab |
| `OFS` | Separador de campos de salida. | Espacio |
| `RS` | Separador de registros de entrada. | Nueva línea |
| `ORS` | Separador de registros de salida. | Nueva línea |
| `FILENAME` | Nombre del archivo en proceso. | — |
| `ARGC` | Número de argumentos de línea de comandos. | — |
| `ARGV` | Array de argumentos de línea de comandos. | — |
| `SUBSEP` | Separador de subíndices en arrays multidimensionales. | `\034` |
| `RSTART` | Posición inicial de la última coincidencia de `match()`. | 0 |
| `RLENGTH` | Longitud de la última coincidencia de `match()`. | -1 |
| `ENVIRON` | Array con las variables de entorno. | — |
| `CONVFMT` | Formato de conversión número→cadena. | `%.6g` |
| `OFMT` | Formato de salida de números con `print`. | `%.6g` |

---

## Operadores

| Categoría | Operadores | Ejemplo |
|-----------|-----------|---------|
| Aritméticos | `+ - * / % ^` (`**`) | `awk '{print $1*$2}'` |
| Asignación | `= += -= *= /= %= ^=` | `awk '{s+=$1} END{print s}'` |
| Comparación | `< <= > >= == != ` | `awk '$3 >= 100'` |
| Lógicos | `&& \|\| !` | `awk '$2>0 && $3<10'` |
| Coincidencia regex | `~` (casa), `!~` (no casa) | `awk '$1 ~ /^root/'` |
| Ternario | `cond ? a : b` | `awk '{print ($1>0)?"pos":"neg"}'` |
| Concatenación | (yuxtaposición) | `awk '{print $1 "-" $2}'` |
| In (pertenencia) | `clave in array` | `awk '{if($1 in v)print}'` |
| Incremento/decremento | `++ --` | `awk '{c[$1]++}'` |

---

## Estructuras de control

| Construcción | Descripción | Ejemplo |
|--------------|-------------|---------|
| `if (cond) … else …` | Condicional. | `awk '{if($1>0) print "+" ; else print "-"}'` |
| `while (cond) {}` | Bucle mientras. | `awk '{i=1; while(i<=NF){print $i; i++}}'` |
| `do {} while (cond)` | Bucle con al menos una iteración. | `awk 'BEGIN{i=0; do{print i; i++}while(i<3)}'` |
| `for (i=0; i<n; i++) {}` | Bucle for clásico. | `awk '{for(i=1;i<=NF;i++) s+=$i; print s}'` |
| `for (clave in array) {}` | Itera las claves de un array. | `awk '{c[$1]++} END{for(k in c) print k,c[k]}'` |
| `break` / `continue` | Rompe / salta iteración. | `awk '{for(i=1;i<=NF;i++){if($i=="x")break}}'` |
| `next` | Pasa al siguiente registro, sin más reglas. | `awk '/^#/{next} {print}'` |
| `nextfile` | Pasa al siguiente fichero de entrada (GNU). | `awk 'FNR>5{nextfile} {print}'` |
| `exit [código]` | Termina; ejecuta el bloque `END` antes de salir. | `awk 'NR==100{exit 0}'` |

---

## Salida con formato

| Función / sentencia | Descripción | Ejemplo |
|---------------------|-------------|---------|
| `print a, b` | Imprime argumentos separados por `OFS` y terminados en `ORS`. | `awk '{print $1, $3}'` |
| `printf "fmt", …` | Salida con formato estilo C (`%s %d %f %x %c %%`, anchos `%-10s`). | `awk '{printf "%-10s %5d\n",$1,$2}'` |
| `sprintf("fmt", …)` | Devuelve la cadena formateada (no imprime). | `awk '{x=sprintf("%03d",$1); print x}'` |
| `print > "fichero"` | Redirige la salida a un fichero (trunca al abrir). | `awk '{print > "salida.txt"}'` |
| `print >> "fichero"` | Añade al final de un fichero. | `awk '{print >> "log.txt"}'` |
| `print \| "cmd"` | Envía la salida por tubería a un comando. | `awk '{print $1 \| "sort"}'` |

---

## Funciones de cadena

| Función | Descripción | Ejemplo |
|---------|-------------|---------|
| `length(str)` | Longitud de la cadena (o `length` = longitud de `$0`). | `length($1)` |
| `substr(str, inicio[, long])` | Subcadena (índice basado en 1). | `substr($0, 1, 3)` |
| `index(str, sub)` | Posición de `sub` en `str` (0 si no está). | `index($1, "abc")` |
| `split(str, arr[, sep])` | Divide `str` en `arr`; devuelve el nº de elementos. | `split($1, a, ":")` |
| `tolower(str)` / `toupper(str)` | Cambio de mayúsculas/minúsculas. | `toupper($1)` |
| `sub(regex, rep[, dest])` | Reemplaza la primera coincidencia. | `sub(/a/, "b", $0)` |
| `gsub(regex, rep[, dest])` | Reemplaza todas las coincidencias; devuelve el nº de sustituciones. | `gsub(/a/, "b", $0)` |
| `gensub(regex, rep, h[, t])` | Reemplazo con grupos `\1…\9` sin modificar el original (GNU). | `gensub(/(.)(.)/,"\\2\\1","g",$0)` |
| `match(str, regex)` | Posición de la coincidencia; fija `RSTART` y `RLENGTH`. | `match($0, /[0-9]+/)` |
| `sprintf(fmt, …)` | Formatea una cadena. | `sprintf("%d-%d",$1,$2)` |

---

## Funciones numéricas y de utilidad

| Función | Descripción | Ejemplo |
|---------|-------------|---------|
| `int(x)` | Parte entera. | `int($1/60)` |
| `sqrt(x)` | Raíz cuadrada. | `sqrt($1)` |
| `exp(x)` / `log(x)` | Exponencial / logaritmo natural. | `log($1)` |
| `sin(x)` `cos(x)` `atan2(y,x)` | Trigonométricas. | `atan2($2,$1)` |
| `rand()` / `srand([sem])` | Aleatorio [0,1) / fija la semilla. | `BEGIN{srand(); print rand()}` |
| `system("cmd")` | Ejecuta un comando del shell; devuelve su código de salida. | `system("mkdir -p out")` |
| `close("fichero\|cmd")` | Cierra un fichero o tubería abierta. | `close("salida.txt")` |
| `getline` | Lee el siguiente registro manualmente (de entrada, fichero o comando). | `while(("date" \| getline d)>0) print d` |
| `fflush([f])` | Vacía los buffers de salida. | `fflush()` |

---

## Arrays

awk solo tiene arrays asociativos (claves = cadenas), aunque admite índices numéricos.

| Operación | Sintaxis | Ejemplo |
|-----------|----------|---------|
| Asignar | `arr[clave] = valor` | `c["error"]++` |
| Comprobar pertenencia | `clave in arr` | `if ("x" in c) …` |
| Iterar claves | `for (k in arr) …` | `for (k in c) print k, c[k]` |
| Eliminar elemento | `delete arr[clave]` | `delete c["tmp"]` |
| Vaciar todo el array | `delete arr` (gawk) | `delete c` |
| Multidimensional | `arr[i,j]` (claves unidas por `SUBSEP`) | `m[$1,$2]++` |
| Comprobar par multidim. | `(i,j) in arr` | `if (($1,$2) in m) …` |
| Ordenar in situ | `asort(arr)` / `asorti(arr,dst)` (gawk) | `n=asort(c)` |

```awk
# Contar y ordenar por clave antes de imprimir (gawk)
{ freq[$1]++ }
END { n=asorti(freq, claves); for (i=1;i<=n;i++) print claves[i], freq[claves[i]] }
```

---

## Funciones definidas por el usuario

```awk
function nombre(param1, param2,    local1) {  # locales tras espacios extra
    ...
    return valor
}
```

| Concepto | Descripción | Ejemplo |
|----------|-------------|---------|
| Definición | `function f(a,b) { return a+b }` | Se invoca como `f(1,2)`. |
| Variables locales | Parámetros extra (por convención, separados con espacios) actúan como locales. | `function suma(arr,   i,s){for(i in arr)s+=arr[i];return s}` |
| Paso de arrays | Los arrays se pasan **por referencia**; los escalares por valor. | `procesa(datos)` |
| `return` | Devuelve un valor (opcional). | `return n*2` |

---

## getline (lectura manual)

`getline` lee registros bajo demanda fuera del bucle principal. Devuelve `1` (éxito), `0` (EOF) o `-1` (error).

| Forma | Origen | Efecto sobre variables |
|-------|--------|------------------------|
| `getline` | Siguiente registro de la entrada | Actualiza `$0`, `NF`, `NR`, `FNR` |
| `getline var` | Siguiente registro de la entrada | Solo `var`, `NR`, `FNR` |
| `getline < "fichero"` | Fichero | `$0`, `NF` |
| `getline var < "fichero"` | Fichero | Solo `var` |
| `"cmd" \| getline` | Salida de un comando | `$0`, `NF` |
| `"cmd" \| getline var` | Salida de un comando | Solo `var` |

```awk
# Leer la fecha del sistema dentro de awk
BEGIN { "date +%F" | getline hoy; print "Informe:", hoy }
```

---

## Separadores avanzados (gawk)

| Variable / opción | Función | Ejemplo |
|-------------------|---------|---------|
| `FS="\t"` | Separador de campo como tabulador. | `awk -F'\t'` |
| `FS=","` con `FPAT` | `FPAT` define los campos *por su contenido* (útil para CSV con comas entrecomilladas). | `awk -v FPAT='([^,]+)\|("[^"]+")' '{print $1}'` |
| `FIELDWIDTHS="3 5 8"` | Campos de ancho fijo (posiciones), ignora `FS`. | `awk -v FIELDWIDTHS='5 3 10'` |
| `RS=""` | Modo párrafo: registros separados por líneas en blanco. | `awk 'BEGIN{RS=""} {print NF}'` |
| `RS="\0"` | Registros terminados en NUL (con `find -print0`). | `awk 'BEGIN{RS="\0"}'` |

---

## Funciones de tiempo y bits (gawk)

| Función | Descripción | Ejemplo |
|---------|-------------|---------|
| `systime()` | Timestamp Unix actual. | `print systime()` |
| `strftime(fmt[, ts])` | Formatea un timestamp. | `strftime("%Y-%m-%d", systime())` |
| `mktime("YYYY MM DD HH MM SS")` | Convierte componentes a timestamp. | `mktime("2026 06 02 0 0 0")` |
| `and/or/xor/lshift/rshift` | Operaciones de bits. | `print and(0xff, 0x0f)` |
| `toupper/tolower` | (Ya en funciones de cadena.) | — |

---

## Casos de uso comunes

```bash
# Imprimir un campo concreto
awk '{print $2}' archivo.txt

# Cambiar separador de entrada y salida
awk -F':' 'BEGIN{OFS=" -> "} {print $1, $7}' /etc/passwd

# Sumar una columna
awk '{sum+=$1} END {print sum}' nums.txt

# Promedio de una columna
awk '{s+=$1; n++} END {print s/n}' nums.txt

# Filtrar por condición numérica
awk '$3 > 1000 {print $1, $3}' datos.txt

# Eliminar líneas duplicadas conservando el orden
awk '!seen[$0]++' archivo.txt

# Contar ocurrencias por clave (group-by)
awk '{c[$1]++} END {for (k in c) print k, c[k]}' log.txt

# Imprimir líneas entre dos patrones
awk '/INICIO/,/FIN/' fichero.txt

# Tabular salida con printf
awk '{printf "%-15s %6d\n", $1, $2}' datos.txt

# Pasar variable externa
awk -v dia="$(date +%F)" 'BEGIN{print "Informe", dia}'

# Imprimir el último campo de cada línea
awk '{print $NF}' fichero.txt

# Extraer columnas reordenadas (lo que cut no puede)
awk -F, '{print $3, $1}' datos.csv
```
