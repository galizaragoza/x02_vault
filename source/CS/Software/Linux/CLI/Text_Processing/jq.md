# Guía Técnica: jq

`jq` es un procesador de JSON por línea de comandos, ligero y flexible, diseñado para filtrar, transformar y estructurar datos estructurados. Funciona como un filtro que toma una entrada de datos, la procesa mediante una expresión de consulta y devuelve el resultado en la salida estándar. Su lenguaje interno permite realizar desde selecciones simples hasta operaciones lógicas y aritméticas complejas sobre flujos de datos JSON.

---

## Opciones de Control de Salida (Output Formatting)

Estas opciones modifican la apariencia y la estructura de los datos que `jq` imprime tras el procesamiento.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-c` / `--compact-output`|Imprime cada objeto JSON en una sola línea en lugar de usar formato "pretty-print".|`jq -c '.' data.json`|
|`-r` / `--raw-output`|Si el resultado es un string, lo escribe directamente sin comillas y sin escapar caracteres.|`jq -r '.name' user.json`|
|`-j` / `--join-output`|Similar a `-r`, pero no imprime un salto de línea después de cada valor de salida.|`jq -j '.id' data.json`|
|`-M` / `--monochrome-output`|Desactiva el resaltado de sintaxis por colores en la terminal.|`jq -M '.' data.json`|
|`-a` / `--ascii-output`|Fuerza a `jq` a escapar todos los caracteres no ASCII usando secuencias \uXXXX.|`jq -a '.' data.json`|
|`--sort-keys`|Ordena las claves de los objetos alfabéticamente en la salida.|`jq --sort-keys '.' file.json`|
|`--indent n`|Define el número de espacios a utilizar para la indentación (máximo 8).|`jq --indent 4 '.' file.json`|
|`--tab`|Utiliza tabuladores para la indentación en lugar de espacios.|`jq --tab '.' file.json`|

---

## Opciones de Control de Entrada (Input Parsing)

Parámetros que definen cómo `jq` debe interpretar los archivos o flujos de datos entrantes.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-n` / `--null-input`|No lee ninguna entrada; el filtro se ejecuta con `null` como valor inicial. Útil para usar `jq` como calculadora.|`jq -n '1 + 1'`|
|`-R` / `--raw-input`|Lee las líneas de entrada como strings de texto plano en lugar de parsearlas como JSON.|`ls|
|`-s` / `--slurp`|Lee toda la entrada en un array único de objetos antes de aplicar el filtro.|`jq -s '.' multiple_files.json`|
|`--slurpfile var file`|Lee el contenido del archivo indicado y lo asigna a una variable global como un array de JSON.|`jq --slurpfile data list.json '.users + $data' main.json`|
|`--rawfile var file`|Lee el contenido del archivo como un string de texto y lo asigna a una variable global.|`jq --rawfile config env.txt '.cfg = $config'`|
|`-L directory`|Añade un directorio a la ruta de búsqueda de módulos para la sentencia `import`.|`jq -L /scripts/jq 'import "util" as u; u::func'`|

---

## Paso de Variables y Argumentos

Mecanismos para inyectar datos externos dentro del entorno de ejecución de los filtros de `jq`.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`--arg name value`|Pasa un valor de string a la ejecución de `jq` como una variable `$name`.|`jq --arg user "root" '.|
|`--argjson name JSON`|Pasa un objeto o valor JSON válido a la ejecución como una variable `$name`.|`jq --argjson id 101 '.|
|`--args`|Pasa los argumentos restantes como strings posicionales accesibles mediante `$ARGS.positional`.|`jq -n '$ARGS.positional' --args a b c`|
|`--jsonargs`|Pasa los argumentos restantes como objetos JSON a `$ARGS.positional`.|`jq -n '$ARGS.positional' --jsonargs '{"a":1}'`|

---

## Control de Flujo y Depuración

Opciones técnicas para gestionar el comportamiento interno del motor de procesamiento.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-e` / `--exit-status`|Establece el código de salida basado en el valor del último resultado (0 si es verdadero/no nulo, 1 o 4 en otros casos).|`jq -e '.error' response.json`|
|`--run-tests`|Ejecuta pruebas internas (normalmente utilizado solo para desarrollo de `jq`).|`jq --run-tests tests.txt`|
|`--unbuffered`|Fuerza a `jq` a vaciar el buffer de salida inmediatamente después de cada valor.|`tail -f log.json|
|`-S` / `--stream`|Parsea la entrada en modo streaming, devolviendo arrays que representan la ruta y el valor. Útil para archivos masivos.|`jq --stream '.' huge_file.json`|
|`--seq`|Utiliza el formato de secuencia de texto JSON (RFC 7464), separando elementos con caracteres RS.|`jq --seq '.' data.json`|

---

## Sintaxis de Filtros (Operaciones Internas)

Aunque no son flags de la CLI, estas son las herramientas de filtrado fundamentales que componen el argumento principal.

|**Operación**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`.`|El filtro de identidad: devuelve la entrada sin cambios.|`jq '.' data.json`|
|`.key`|Extrae el valor asociado a una clave específica de un objeto.|`jq '.address.city' data.json`|
|`.[index]`|Accede al elemento de un array en el índice especificado.|`jq '.[0]' array.json`|
|`[]`|Itera sobre todos los elementos de un array o los valores de un objeto.|`jq '.items[]' data.json`|
|`|`|Pipe: pasa el resultado del filtro de la izquierda como entrada al de la derecha.|
|`map(filter)`|Aplica un filtro a cada elemento de un array de entrada.|`jq 'map(. + 1)' [1,2,3]`|
|`select(boolean_expr)`|Filtra la entrada y solo mantiene aquellos elementos que cumplen la condición.|`jq '.[]|