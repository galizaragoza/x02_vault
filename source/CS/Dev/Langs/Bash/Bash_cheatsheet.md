 

| **Operador**         | **Función**                                                         | **Sintaxis de Ejemplo**                                 |
| -------------------- | ------------------------------------------------------------------- | ------------------------------------------------------- |
| `>`                  | Redirige el stdout del comando al archivo escogido (sobreescribe)   | `ls -la > archivos.txt`                                 |
| `>>`                 | Redirige el stdout del comando al archivo escogido (añade al final) | `echo "nuevo" >> log.txt`                               |
| `\|` (pipe)          | Envía el stdout del comando 1 al stdin del comando 2 (stackeable)   | `cat file.txt \| grep "error"`                          |
| `2>`                 | Redirige el stderror al archivo escogido                            | `cmd > salida.txt 2> errores.txt`                       |
| `2>&1`               | Redirige el stderror al mismo lugar que el stdout                   | `cmd > todo.log 2>&1`                                   |
| `&>`                 | Redirige tanto stdout como stderr (bash)                            | `cmd &> salida_completa.log`                            |
| `1>&2`               | Redirige stdout a stderr                                            | `echo "Error" 1>&2`                                     |
| `<`                  | Redirige stdin desde un archivo                                     | `sort < datos.txt`                                      |
| `<<` (here-document) | Redirige stdin desde texto en línea                                 | `cat << EOF\nTexto\nEOF`                                |
| `<<<` (here-string)  | Redirige stdin desde una cadena                                     | `grep "patron" <<< "$variable"`                         |
| `&`                  | Ejecuta el comando en background                                    | `sleep 60 &`                                            |
| `&&`                 | Operador AND lógico - ejecuta cmd2 solo si cmd1 tiene éxito         | `make && make install`                                  |
| `\|\|`               | Operador OR lógico - ejecuta cmd2 solo si cmd1 falla                | `cd /directorio \|\| mkdir /directorio`                 |
| `;`                  | Separa comandos - ejecuta secuencialmente                           | `echo Hola; echo Mundo`                                 |
| `&>` y `>&`          | Redirige stdout y stderr (formas equivalentes)                      | `cmd &> archivo` o `cmd > archivo 2>&1`                 |
| `\|&`                | Pipe que incluye stderr (equivalente a `2>&1 \|`)                   | `cmd1 \|& cmd2`                                         |
| `<(...)`             | Process substitution (input) - trata output como archivo            | `diff <(ls dir1) <(ls dir2)`                            |
| `>(...)`             | Process substitution (output) - trata input como archivo            | `tar czf >(ssh host "cat > backup.tar.gz") directorio/` |
| `()`                 | Agrupa comandos en un subshell                                      | `(cd /tmp && ls) > salida.txt`                          |
| `{}`                 | Agrupa comandos en el shell actual                                  | `{ echo Inicio; ls; echo Fin; } > log.txt`              |
| `!`                  | Niega el código de salida                                           | `! grep -q "texto" archivo && echo "No encontrado"`     |
| `$()` o `` ` ` ``    | Command substitution - sustituye por la salida del comando          | `echo "Hoy es $(date)"`                                 |
| `$(<archivo)`        | Sustituye por el contenido del archivo (más eficiente que cat)      | `contenido=$(<archivo.txt)`                             |


# Wildcards

| `?`  | Representa un único caracter          | ?at     | cat, hat... |
| ---- | ------------------------------------- | ------- | ----------- |
| `*`  | Representa varios caracteres          | c*p     | cup, chip   |
| `[]` | Matchea los caracteres entre brackets | c[o,u]p | chop, chup  |

# Operators

|                           | **Comparación**                       |
| ------------------------- | ------------------------------------- |
| `-eq`                     | Igual que                             |
| `-ne`                     | Distinto que                          |
| `-lt`                     | Menor que                             |
| `-le`                     | Menor o igual que                     |
| `-gt`                     | Mayor que                             |
| `-ge`                     | Mayor o igual que                     |
|                           |                                       |
|                           | **Comparación de strings**            |
| `=`                       | Igual a                               |
| `!=` (`!\=`)              | Distinto que                          |
| `<`                       | Menor que (orden alfabético ASCII)    |
| `>`                       | Mayor que (orden alfabético ASCII)    |
|                           |                                       |
|                           | **Aritmética**                        |
| `+`                       | Suma                                  |
| `-`                       | Resta                                 |
| `*`                       | Multiplicación                        |
| `/`                       | División                              |
| `%`                       | Resta de una división                 |
| `bc` o `awk`              | Potencias, etc.                       |
|                           |                                       |
|                           | **Boolean**                           |
| `&&`                      | AND                                   |
| `\|\|` (doble barra \|\|) | OR                                    |
| `!`                       | NOT                                   |
|                           |                                       |
|                           | **File Test**                         |
| `-e`                      | Comprueba si un archivo existe        |
| `-d`                      | Comprueba si un dir existe            |
| `-f`                      | Comprueba si un archivo es regular    |
| `-s`                      | Comprueba si un archivo no esta vacío |

# CLI shortkeys
| **Keystroke** | **Action**                                        |
| ------------- | ------------------------------------------------- |
| CTRL-B        | Move the cursor left                              |
| CTRL-F        | Move the cursor right                             |
| CTRL-P        | View the previous command (or move the cursor up) |
| CTRL-N        | View the next command (or move the cursor down)   |
| CTRL-A        | Move the cursor to the beginning of the line      |
| CTRL-E        | Move the cursor to the end of the line            |
| CTRL-W        | Erase the preceding word                          |
| CTRL-U        | Erase from cursor to beginning of line            |
| CTRL-K        | Erase from cursor to end of line                  |
| CTRL-Y        | Paste erased text (for example, from CTRL-U)      |
| CTRL-L        | Clear the screen                                  |
| CTRL-R        | Reverse-incremental search in command history     |

# Built-ins
|**Comando**|**Función**|**Sintaxis de Ejemplo**|
|---|---|---|
|`break`|Sale de un bucle `for`, `while`, o `until`|`while true; do if [ $i -eq 10 ]; then break; fi; done`|
|`continue`|Salta a la siguiente iteración de un bucle|`for i in {1..10}; do if [ $i -eq 5 ]; then continue; fi; echo $i; done`|
|`exit`|Termina el script o shell con código de salida|`exit 0` (éxito) o `exit 1` (error)|
|`return`|Sale de una función con código de retorno|`function foo() { return 42; }`|
|`fg`|Trae un job al foreground|`fg %1` o `fg` (último job)|
|`bg`|Reanuda un job en background|`bg %2`|
|`jobs`|Lista jobs en background|`jobs -l`|
|`wait`|Espera a que jobs en background terminen|`wait %1` o `wait` (todos)|
|`disown`|Remueve jobs de la tabla de jobs del shell|`disown %1`|

## Variables y Entorno

|**Comando**|**Función**|**Sintaxis de Ejemplo**|
|---|---|---|
|`declare`|Declara variables con atributos|`declare -i numero=5` (entero)|
|`typeset`|Sinónimo de `declare`|`typeset -r CONSTANTE=10` (solo lectura)|
|`local`|Declara variable local a una función|`function foo() { local var="local"; }`|
|`export`|Hace variable disponible a procesos hijos|`export PATH="$PATH:/nuevo/dir"`|
|`readonly`|Hace variable de solo lectura|`readonly PI=3.1416`|
|`unset`|Elimina variable o función|`unset VARIABLE`|
|`set`|Muestra/establece opciones del shell|`set -o errexit` o `set -x`|
|`shopt`|Gestiona opciones del shell|`shopt -s nocaseglob` (case-insensitive)|

## Entrada/Salida

|**Comando**|**Función**|**Sintaxis de Ejemplo**|
|---|---|---|
|`read`|Lee entrada desde stdin o archivo|`read -p "Nombre: " nombre`|
|`echo`|Imprime texto a stdout|`echo -e "Línea1\nLínea2"`|
|`printf`|Imprime texto con formato|`printf "Nombre: %s, Edad: %d\n" "$nombre" $edad`|
|`exec`|Reemplaza shell actual o redirige I/O|`exec 2> errores.log`|

## Evaluación y Expansión

|**Comando**|**Función**|**Sintaxis de Ejemplo**|
|---|---|---|
|`eval`|Evalúa y ejecuta comandos de una cadena|`eval "ls $directorio"`|
|`test` o `[ ]`|Evalúa expresiones condicionales|`[ -f archivo.txt ] && echo "Existe"`|
|`[[ ]]`|Evaluación condicional extendida (bash)|`[[ $var == *patron* ]]`|
|`(( ))`|Evaluación aritmética|`(( resultado = a + b ))`|
|`let`|Realiza operaciones aritméticas|`let "suma = a + b"`|
|`source` o `.`|Ejecuta comandos desde un archivo|`source config.sh` o `. ~/.bashrc`|

## Manejo de Directorios

|**Comando**|**Función**|**Sintaxis de Ejemplo**|
|---|---|---|
|`cd`|Cambia directorio de trabajo|`cd /ruta/o ~/directorio`|
|`pushd`|Guarda directorio actual y cambia|`pushd /nuevo/dir`|
|`popd`|Regresa al directorio guardado|`popd`|
|`dirs`|Muestra la pila de directorios|`dirs -v`|

## Otros Built-ins Esenciales

|**Comando**|**Función**|**Sintaxis de Ejemplo**|
|---|---|---|
|`trap`|Captura y maneja señales|`trap "echo 'Interrumpido'" SIGINT`|
|`shift`|Desplaza parámetros posicionales|`shift 2` (elimina $1 y $2)|
|`getopts`|Procesa opciones de línea de comandos|`while getopts ":a:b:" opt; do case $opt in ...`|
|`alias`|Crea alias (ya cubierto en sección anterior)|`alias ll='ls -la'`|
|`type`|Muestra información de comandos (ya cubierto)|`type -a ls`|
|`command`|Ejecuta comando ignorando funciones/alias|`command ls` (versión real, no alias)|
|`builtin`|Ejecuta un built-in específicamente|`builtin cd /directorio`|
|`times`|Muestra tiempos de ejecución acumulados|`times`|
|`ulimit`|Controla límites de recursos del shell|`ulimit -n 2048` (máx archivos abiertos)|
|`hash`|Maneja tabla hash de comandos encontrados|`hash -r` (limpia cache)|

## Built-ins para Arrays (Bash 4+)

|**Comando**|**Función**|**Sintaxis de Ejemplo**|
|---|---|---|
|`mapfile` o `readarray`|Lee líneas en array|`mapfile -t lineas < archivo.txt`|
|`read -a`|Lee palabras en array|`read -a array <<< "a b c d"`|


# Expansión de parámetros (Parameter Expansion)

Transformaciones sobre el valor de una variable en el momento de la expansión, sin invocar herramientas externas (`sed`, `cut`...). Son más rápidas y portables dentro de scripts. Asumiendo `VAR="/ruta/al/fichero.tar.gz"` y `PATH_VAR="usr:bin:local"`.

## Valores por defecto y comprobación

| Expansión | Función | Ejemplo |
|-----------|---------|---------|
| `${VAR}` | Valor de la variable (forma explícita; permite pegar texto: `${VAR}_suffix`). | `echo "${VAR}.bak"` |
| `${VAR:-def}` | Usa `def` si `VAR` está vacía o sin definir. No modifica `VAR`. | `echo "${EDITOR:-vi}"` |
| `${VAR:=def}` | Asigna `def` a `VAR` si está vacía/sin definir, y la expande. | `: "${TMPDIR:=/tmp}"` |
| `${VAR:?msg}` | Error a stderr con `msg` y sale si `VAR` está vacía/sin definir. | `: "${API_KEY:?falta API_KEY}"` |
| `${VAR:+alt}` | Usa `alt` solo si `VAR` está definida y no vacía. | `echo "${DEBUG:+--verbose}"` |

> Sin los dos puntos (`${VAR-def}`, etc.), la condición solo distingue *sin definir* de *definida* (una variable vacía cuenta como definida).

## Longitud, subcadena e indirección

| Expansión | Función | Ejemplo |
|-----------|---------|---------|
| `${#VAR}` | Longitud (número de caracteres) del valor. | `echo ${#VAR}` |
| `${VAR:offset}` | Subcadena desde `offset` (basado en 0) hasta el final. | `echo "${VAR:6}"` |
| `${VAR:offset:len}` | Subcadena de `len` caracteres desde `offset`. | `echo "${VAR:0:5}"` |
| `${VAR: -N}` | Últimos N caracteres (ojo al espacio antes del `-`). | `echo "${VAR: -7}"` → `.tar.gz` |
| `${!VAR}` | Indirección: expande a la variable cuyo nombre contiene `VAR`. | `n=HOME; echo "${!n}"` |
| `${!pre*}` / `${!pre@}` | Nombres de variables que empiezan por `pre`. | `echo ${!BASH*}` |

## Eliminación de prefijos y sufijos (string trimming)

Eliminan la parte del valor que casa un patrón glob. Clave para manipular rutas y nombres sin `basename`/`dirname`.

| Expansión | Función | Ejemplo (`VAR="/ruta/al/fichero.tar.gz"`) |
|-----------|---------|-------------------------------------------|
| `${VAR#patrón}` | Elimina el prefijo más **corto** que case `patrón`. | `${VAR#*/}` → `ruta/al/fichero.tar.gz` |
| `${VAR##patrón}` | Elimina el prefijo más **largo** que case `patrón` (≈ `basename`). | `${VAR##*/}` → `fichero.tar.gz` |
| `${VAR%patrón}` | Elimina el sufijo más **corto** que case `patrón`. | `${VAR%.*}` → `/ruta/al/fichero.tar` |
| `${VAR%%patrón}` | Elimina el sufijo más **largo** que case `patrón`. | `${VAR%%.*}` → `/ruta/al/fichero` |

Ejemplos sobre `PATH_VAR="usr:bin:local"`:

| Expansión | Resultado | Equivale a |
|-----------|-----------|------------|
| `${PATH_VAR%%:*}` | `usr` | Primer campo (corta desde el primer `:` hasta el final). |
| `${PATH_VAR##*:}` | `local` | Último campo (corta hasta el último `:`). |
| `${PATH_VAR#*:}` | `bin:local` | Quita el primer campo. |
| `${PATH_VAR%:*}` | `usr:bin` | Quita el último campo. |

## Sustitución de patrón

| Expansión | Función | Ejemplo (`s="a.b.c.d"`) |
|-----------|---------|------------------------|
| `${VAR/pat/rep}` | Sustituye la **primera** coincidencia de `pat` por `rep`. | `${s/./_}` → `a_b.c.d` |
| `${VAR//pat/rep}` | Sustituye **todas** las coincidencias (global). | `${s//./_}` → `a_b_c_d` |
| `${VAR/#pat/rep}` | Sustituye solo si `pat` casa al **inicio**. | `${s/#a/X}` → `X.b.c.d` |
| `${VAR/%pat/rep}` | Sustituye solo si `pat` casa al **final**. | `${s/%d/X}` → `a.b.c.X` |
| `${VAR/pat/}` | Elimina la coincidencia (reemplazo vacío). | `${s//./}` → `abcd` |

## Modificación de mayúsculas/minúsculas y transformación (Bash 4+)

| Expansión | Función | Ejemplo (`w="hola MUNDO"`) |
|-----------|---------|---------------------------|
| `${VAR^}` | Primera letra a mayúscula. | `${w^}` → `Hola MUNDO` |
| `${VAR^^}` | Todo a mayúsculas. | `${w^^}` → `HOLA MUNDO` |
| `${VAR,}` | Primera letra a minúscula. | `${w,}` → `hola MUNDO` |
| `${VAR,,}` | Todo a minúsculas. | `${w,,}` → `hola mundo` |
| `${VAR@Q}` | Valor entrecomillado de forma reutilizable por el shell. | `${w@Q}` → `'hola MUNDO'` |
| `${VAR@U}` / `${VAR@L}` | A mayúsculas / minúsculas (Bash 5+). | `${w@U}` |
| `${VAR@A}` | Genera una asignación que recrea la variable y atributos. | `declare -p` equivalente |

---

# Expansión de llaves (Brace Expansion)

Genera cadenas arbitrarias **antes** de cualquier otra expansión; no depende de ficheros existentes (a diferencia de los wildcards).

| Patrón | Función | Ejemplo → Resultado |
|--------|---------|---------------------|
| `{a,b,c}` | Lista de alternativas. | `echo {rojo,verde,azul}` → `rojo verde azul` |
| `pre{a,b}post` | Con prefijo/sufijo. | `echo arch_{1,2}.log` → `arch_1.log arch_2.log` |
| `{N..M}` | Rango numérico (ascendente o descendente). | `echo {1..5}` → `1 2 3 4 5` |
| `{N..M..S}` | Rango con paso `S`. | `echo {0..10..2}` → `0 2 4 6 8 10` |
| `{a..z}` | Rango de caracteres. | `echo {a..e}` → `a b c d e` |
| `{01..03}` | Rango con relleno de ceros. | `echo {01..03}` → `01 02 03` |
| Anidado | Combinaciones. | `echo {A,B}{1,2}` → `A1 A2 B1 B2` |

```bash
# Backup rápido de un fichero
cp config.yml{,.bak}            # → cp config.yml config.yml.bak
# Crear estructura de directorios
mkdir -p proyecto/{src,test,docs}
```

---

# Expansión aritmética

| Construcción | Función | Ejemplo |
|--------------|---------|---------|
| `$(( expr ))` | Evalúa y **sustituye** por el resultado entero. | `echo $(( (2+3) * 4 ))` → `20` |
| `(( expr ))` | Evalúa sin sustituir; fija `$?` (0 si resultado ≠ 0). | `(( i++ ))`, `if (( a > b )); then` |
| `**` | Potencia. | `echo $(( 2 ** 10 ))` → `1024` |
| `% / * + -` | Módulo, división entera, etc. | `echo $(( 17 % 5 ))` → `2` |
| `<< >> & \| ^ ~` | Operadores a nivel de bit. | `echo $(( 1 << 4 ))` → `16` |
| `b#n` | Número `n` en base `b`. | `echo $(( 16#ff ))` → `255`, `echo $(( 2#1010 ))` → `10` |
| `c?a:b` | Ternario. | `echo $(( x>0 ? 1 : -1 ))` |

> Dentro de `(( ))` y `$(( ))` no se necesita `$` para leer variables (`(( total = a + b ))`), y la división es entera (usar `bc -l` o `awk` para decimales).

---

# Evaluación condicional extendida `[[ ]]`

Superset de `[ ]` (test) con menos sorpresas de entrecomillado y operadores extra (solo Bash/Zsh, no POSIX `sh`).

| Operador | Función | Ejemplo |
|----------|---------|---------|
| `==` / `!=` | Igualdad con **coincidencia de patrón glob** (lado derecho sin comillas). | `[[ $f == *.txt ]]` |
| `=~` | Coincidencia con **expresión regular** (ERE); grupos en `${BASH_REMATCH[@]}`. | `[[ $ip =~ ^[0-9.]+$ ]]` |
| `<` / `>` | Orden lexicográfico (sin escapar, a diferencia de `[ ]`). | `[[ "$a" < "$b" ]]` |
| `-z` / `-n` | Cadena vacía / no vacía. | `[[ -z $var ]]` |
| `&&` / `\|\|` | AND / OR lógico dentro del propio test. | `[[ -f $f && -r $f ]]` |
| `-nt` / `-ot` | Fichero más nuevo / más antiguo que otro. | `[[ src.c -nt bin ]]` |
| `-ef` | Dos rutas refieren al mismo inodo (mismo fichero/hardlink). | `[[ a -ef b ]]` |

```bash
# Validar formato con regex y capturar grupos
if [[ "2026-06-02" =~ ^([0-9]{4})-([0-9]{2})-([0-9]{2})$ ]]; then
  echo "Año: ${BASH_REMATCH[1]}"
fi
```

---

# Arrays

## Indexados

| Operación | Sintaxis | Ejemplo |
|-----------|----------|---------|
| Declarar | `arr=(a b c)` o `declare -a arr` | `frutas=(manzana pera uva)` |
| Asignar índice | `arr[i]=valor` | `frutas[3]=kiwi` |
| Acceder | `${arr[i]}` | `echo "${frutas[0]}"` |
| Todos los elementos | `"${arr[@]}"` (cada uno como palabra) | `for f in "${frutas[@]}"; do …` |
| Todos los índices | `"${!arr[@]}"` | `echo "${!frutas[@]}"` |
| Número de elementos | `${#arr[@]}` | `echo ${#frutas[@]}` |
| Slice | `${arr[@]:offset:len}` | `echo "${frutas[@]:1:2}"` |
| Añadir | `arr+=(nuevo)` | `frutas+=(mango)` |
| Eliminar elemento | `unset 'arr[i]'` | `unset 'frutas[1]'` |

## Asociativos (Bash 4+)

| Operación | Sintaxis | Ejemplo |
|-----------|----------|---------|
| Declarar | `declare -A mapa` (**obligatorio**) | `declare -A edad` |
| Asignar | `mapa[clave]=valor` | `edad[ana]=30` |
| Acceder | `${mapa[clave]}` | `echo "${edad[ana]}"` |
| Todas las claves | `"${!mapa[@]}"` | `for k in "${!edad[@]}"; do …` |
| Todos los valores | `"${mapa[@]}"` | `echo "${edad[@]}"` |

---

# Globbing extendido y opciones de shell

`extglob` (activar con `shopt -s extglob`) añade patrones tipo regex a la expansión de nombres:

| Patrón | Función | Ejemplo |
|--------|---------|---------|
| `?(patrón)` | Cero o una ocurrencia. | `ls ?(fichero).txt` |
| `*(patrón)` | Cero o más. | `rm *(.bak)` |
| `+(patrón)` | Una o más. | `ls +([0-9]).log` |
| `@(p1\|p2)` | Exactamente una de las alternativas. | `ls @(jpg\|png\|gif)` |
| `!(patrón)` | Cualquier cosa **excepto** el patrón (negación). | `rm !(*.keep)` |
| `**` | Globstar: recursivo en subdirectorios (requiere `shopt -s globstar`). | `ls **/*.md` |

Opciones de shell útiles (`shopt`):

| Opción | Efecto |
|--------|--------|
| `nocaseglob` | Globbing insensible a mayúsculas. |
| `nullglob` | Un patrón sin coincidencias se expande a nada (en vez de quedarse literal). |
| `dotglob` | Incluye ficheros ocultos (`.*`) en el globbing. |
| `failglob` | Error si un patrón no casa nada. |

---

# Sourcing vs ejecución

Diferencia clave: ejecutar un script lo corre en un **subproceso** (sus variables/`cd` no afectan al shell actual); *sourcearlo* lo ejecuta en el **shell actual**.

| Forma | Efecto | Ejemplo |
|-------|--------|---------|
| `./script.sh` | Nuevo proceso (necesita `+x` y shebang). Cambios de entorno se pierden al terminar. | `./deploy.sh` |
| `bash script.sh` | Nuevo proceso con `bash` explícito (no requiere `+x`). | `bash deploy.sh` |
| `. ./script.sh` | **Source** (POSIX): ejecuta en el shell actual; conserva variables, funciones y `cd`. | `. ./entorno.sh` |
| `source ./script.sh` | Idéntico a `.` (sinónimo de Bash). | `source ~/.bashrc` |

```bash
# Cargar variables de entorno en la sesión actual
. ./.env            # las variables definidas quedan disponibles después
# Frente a:
./.env              # se exportarían solo dentro del subproceso → se pierden
```

> Patrón habitual: ficheros de configuración (`.bashrc`, `.env`, `lib/*.sh`) se *sourcean* para que sus definiciones persistan; scripts de tarea se ejecutan.

---

# Estructuras de control

## Condicionales

```bash
if [[ $edad -ge 18 ]]; then
  echo "mayor"
elif [[ $edad -ge 13 ]]; then
  echo "adolescente"
else
  echo "menor"
fi

# case (patrón glob, no regex)
case "$opcion" in
  start|s)  echo "arrancando" ;;
  stop)     echo "parando"   ;;
  *)        echo "desconocido" ;;   # default
esac
```

## Bucles

| Forma | Sintaxis |
|-------|----------|
| `for` sobre lista | `for x in a b c; do echo "$x"; done` |
| `for` C-style | `for ((i=0; i<10; i++)); do echo $i; done` |
| `for` sobre ficheros | `for f in *.txt; do …; done` |
| `while` | `while [[ $i -lt 5 ]]; do ((i++)); done` |
| `until` (hasta que sea cierto) | `until ping -c1 host; do sleep 1; done` |
| Leer fichero línea a línea | `while IFS= read -r line; do echo "$line"; done < f.txt` |

> Siempre `IFS= read -r` para no perder espacios ni interpretar `\`. Procesar la salida de un comando: `cmd | while read -r l; do …; done` (ojo: el `while` corre en subshell, las variables no persisten — usar process substitution `while …; done < <(cmd)`).

# Funciones

```bash
saludar() {
  local nombre="${1:-mundo}"   # parámetro posicional con default
  echo "Hola, $nombre"
  return 0                      # 0-255; el "valor" real se da por stdout/echo
}
saludar "Ana"
resultado=$(saludar "Ana")     # capturar la salida
```

| Concepto | Detalle |
|----------|---------|
| Argumentos | `$1`, `$2`… `$@` (todos como palabras), `$#` (cantidad), `$*` (todos como una cadena) |
| Retorno | `return N` solo fija `$?` (código 0-255); para devolver datos usar `echo`/`printf` + `$(...)` |
| Variables locales | `local var=…` evita contaminar el ámbito global |
| Recursión | Permitida; cuidado con la profundidad |

# Señales y trap

| Señal | Nº | Significado |
|-------|----|-------------|
| `SIGINT` | 2 | Ctrl-C (interrupción) |
| `SIGTERM` | 15 | Terminación ordenada (default de `kill`) |
| `SIGKILL` | 9 | Mata sin posibilidad de captura (no atrapable) |
| `SIGHUP` | 1 | Cierre de terminal; usado para recargar daemons |
| `EXIT` | — | Pseudo-señal Bash: se ejecuta al salir del script |

```bash
# Limpieza garantizada al salir (éxito o error)
tmp=$(mktemp)
trap 'rm -f "$tmp"' EXIT
trap 'echo "abortado"; exit 130' INT TERM
```

# Manejo robusto de errores

| Opción | Efecto |
|--------|--------|
| `set -e` (`errexit`) | Aborta el script si un comando falla |
| `set -u` (`nounset`) | Error al usar variable sin definir |
| `set -o pipefail` | Una tubería falla si **cualquier** etapa falla (no solo la última) |
| `set -x` (`xtrace`) | Traza cada comando ejecutado (debug) |

```bash
set -euo pipefail            # cabecera defensiva habitual en scripts serios
IFS=$'\n\t'                  # IFS seguro: evita split por espacios
```

> `set -e` tiene trampas: no dispara dentro de `if`, `&&`, `||` ni en funciones llamadas en esos contextos. No sustituye comprobar `$?` en puntos críticos.

# Descriptores de fichero y redirección avanzada

| Construcción | Función |
|--------------|---------|
| `exec 3< fichero` | Abre el FD 3 para lectura |
| `exec 3> salida` | Abre el FD 3 para escritura |
| `read -u 3 línea` | Lee del FD 3 |
| `exec 3>&-` | Cierra el FD 3 |
| `cmd 2>/dev/null` | Descarta stderr |
| `cmd &>/dev/null` | Descarta stdout y stderr |
| `cmd > >(tee log)` | Envía stdout a un proceso (tee) por process substitution |

# Recursos
### [Bash Man: Process Subsitution](https://www.gnu.org/software/bash/manual/html_node/Process-Substitution.html)
### [Process Subs. StackExg](https://stackoverflow.com/questions/2443085/what-does-command-args-mean-in-the-shell)
### [Bash Man: Shell Parameter Expansion](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)