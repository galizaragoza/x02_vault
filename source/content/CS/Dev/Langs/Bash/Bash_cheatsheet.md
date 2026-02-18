 

|**Operador**|**Función**|**Sintaxis de Ejemplo**|
|---|---|---|
|`>`|Redirige el stdout del comando al archivo escogido (sobreescribe)|`ls -la > archivos.txt`|
|`>>`|Redirige el stdout del comando al archivo escogido (añade al final)|`echo "nuevo" >> log.txt`|
|`\|` (pipe)|Envía el stdout del comando 1 al stdin del comando 2 (stackeable)|`cat file.txt \| grep "error"`|
|`2>`|Redirige el stderror al archivo escogido|`cmd > salida.txt 2> errores.txt`|
|`2>&1`|Redirige el stderror al mismo lugar que el stdout|`cmd > todo.log 2>&1`|
|`&>`|Redirige tanto stdout como stderr (bash)|`cmd &> salida_completa.log`|
|`1>&2`|Redirige stdout a stderr|`echo "Error" 1>&2`|
|`<`|Redirige stdin desde un archivo|`sort < datos.txt`|
|`<<` (here-document)|Redirige stdin desde texto en línea|`cat << EOF\nTexto\nEOF`|
|`<<<` (here-string)|Redirige stdin desde una cadena|`grep "patron" <<< "$variable"`|
|`&`|Ejecuta el comando en background|`sleep 60 &`|
|`&&`|Operador AND lógico - ejecuta cmd2 solo si cmd1 tiene éxito|`make && make install`|
|`\|`|Operador OR lógico - ejecuta cmd2 solo si cmd1 falla|`cd /directorio \| mkdir /directorio`|
|`;`|Separa comandos - ejecuta secuencialmente|`echo Hola; echo Mundo`|
|`&>` y `>&`|Redirige stdout y stderr (formas equivalentes)|`cmd &> archivo` o `cmd > archivo 2>&1`|
|`\|&`|Pipe que incluye stderr (equivalente a `2>&1 \|`)|`cmd1 \|& cmd2`|
|`<(...)`|Process substitution (input) - trata output como archivo|`diff <(ls dir1) <(ls dir2)`|
|`>(...)`|Process substitution (output) - trata input como archivo|`tar czf >(ssh host "cat > backup.tar.gz") directorio/`|
|`()`|Agrupa comandos en un subshell|`(cd /tmp && ls) > salida.txt`|
|`{}`|Agrupa comandos en el shell actual|`{ echo Inicio; ls; echo Fin; } > log.txt`|
|`!`|Niega el código de salida|`! grep -q "texto" archivo && echo "No encontrado"`|
|`$()` o `` ` ` ``|Command substitution - sustituye por la salida del comando|`echo "Hoy es $(date)"`|
|`$(<archivo)`|Sustituye por el contenido del archivo (más eficiente que cat)|`contenido=$(<archivo.txt)`|


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


# Recursos
### [Bash Man: Process Subsitution](https://www.gnu.org/software/bash/manual/html_node/Process-Substitution.html)
### [Process Subs. StackExg](https://stackoverflow.com/questions/2443085/what-does-command-args-mean-in-the-shell)