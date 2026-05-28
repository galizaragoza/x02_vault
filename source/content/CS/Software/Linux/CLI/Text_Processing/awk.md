**
```
awk 'patrón {acción}' archivo.txt
```

# Parámetros

| **Flag/Patrón** | **Función**                                   | **Ejemplo de sintaxis**                    |
| --------------- | --------------------------------------------- | ------------------------------------------ |
| `-F`            | Define separador de campos                    | `awk -F':' '{print $1}' /etc/passwd`       |
| `!seen[$0]++`   | Elimina duplicados preservando orden original | `awk '!seen[$0]++' archivo.txt`            |
| `NR==n`         | Procesa solo línea n                          | `awk 'NR==10 {print}' log.txt`             |
| `/patrón/`      | Filtra líneas que coinciden con regex         | `awk '/FLAGPART:/ {print $NF}' log.txt`    |
| `{print $n}`    | Imprime campo n                               | `awk '{print $2}' archivo.txt`             |
| `BEGIN {}`      | Ejecuta antes de procesar                     | `awk 'BEGIN {FS=":"} {print $1}'`          |
| `END {}`        | Ejecuta al final                              | `awk '{sum+=$1} END {print sum}' nums.txt` |

## Variables predefinidas
| **Variable**     | **Descripción**                                                        | **Valor por Defecto**     |
| ---------------- | ---------------------------------------------------------------------- | ------------------------- |
| `$0`             | Toda la línea actual                                                   | Línea completa de entrada |
| `$1, $2, ... $n` | Campo n-ésimo de la línea                                              | Campos individuales       |
| `NR`             | Número de registro actual (número de línea)                            | Incremental               |
| `NF`             | ....the value of the last field in the current record being processed. | Depende de la línea       |
| `FS`             | Separador de campos (Field Separator)                                  | Espacio o tab             |
| `OFS`            | Separador de campos de salida (Output FS)                              | Espacio                   |
| `RS`             | Separador de registros (Record Separator)                              | Nueva línea               |
| `ORS`            | Separador de registros de salida                                       | Nueva línea               |
| `FILENAME`       | Nombre del archivo actual                                              | -                         |
| `FNR`            | Número de registro en el archivo actual                                | -                         |
| `ARGC`           | Número de argumentos de línea de comandos                              | -                         |
| `ARGV`           | Array de argumentos de línea de comandos                               | -                         |
## Funciones string

| **Función**                       | **Descripción**                  | **Ejemplo**               |
| ----------------------------- | ---------------------------- | --------------------- |
| `length(str)`                 | Longitud de la cadena        | `length($1)`          |
| `substr(str, inicio, long)`   | Subcadena                    | `substr($0, 1, 3)`    |
| `index(str, substr)`          | Posición de subcadena        | `index($1, "abc")`    |
| `split(str, arr, sep)`        | Divide cadena en array       | `split($1, a, ":")`   |
| `tolower(str)`                | Convertir a minúsculas       | `tolower($1)`         |
| `toupper(str)`                | Convertir a mayúsculas       | `toupper($1)`         |
| `gsub(regex, reemplazo, str)` | Reemplazo global             | `gsub(/a/, "b", $0)`  |
| `sub(regex, reemplazo, str)`  | Reemplazo primera ocurrencia | `sub(/a/, "b", $0)`   |
| `match(str, regex)`           | Coincidencia con regex       | `match($0, /[0-9]+/)` |