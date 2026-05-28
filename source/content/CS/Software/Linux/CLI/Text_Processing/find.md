# Parámetros

| Parámetro         | Función                 | Ejemplo de sintaxis                   |
| ----------------- | ----------------------- | ------------------------------------- |
| `-name "pat"`     | Buscar por nombre       | `find / -name "*.conf"`               |
| `-type f/d/l`     | Tipo archivo/dir/enlace | `find . -type f`                      |
| `-perm mode`      | Permisos exactos        | `find . -perm 777`                    |
| `-user name`      | Propietario             | `find /home -user alice`              |
| `-exec cmd {} \;` | Ejecutar comando        | `find . -name "*.tmp" -exec rm {} \;` |
|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-P`|Nunca sigue enlaces simbólicos (predeterminado)|`find -P /ruta`|
|`-L`|Sigue enlaces simbólicos|`find -L /ruta`|
|`-H`|Solo sigue enlaces simbólicos en argumentos|`find -H /ruta`|
|`-D`|Modo debug (opciones: help, tree, stat, opt, rates)|`find -D tree /ruta`|
|`-Olevel`|Nivel de optimización (0-3)|`find -O2 /ruta`|
## Criterios de búsqueda (Tests)

#### Por nombre y tipo

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-name`|Busca por nombre (patrón glob)|`find /ruta -name "*.txt"`|
|`-iname`|Busca por nombre sin distinguir mayúsculas/minúsculas|`find /ruta -iname "archivo*"`|
|`-regex`|Busca usando expresiones regulares|`find /ruta -regex ".*\.txt$"`|
|`-iregex`|Expresión regular sin distinguir mayúsculas/minúsculas|`find /ruta -iregex ".*\.(txt\|pdf)$"`|
|`-type`|Filtra por tipo de archivo (f, d, l, b, c, p, s)|`find /ruta -type f` (archivos regulares)|
|`-path`|Busca por ruta completa|`find /ruta -path "*/tmp/*"`|
|`-ipath`|Ruta completa sin distinguir mayúsculas/minúsculas|`find /ruta -ipath "*/TEMP/*"`|

#### Por tamaño

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-size`|Busca por tamaño de archivo|`find /ruta -size +1M` (más de 1MB)|
|`-empty`|Archivos o directorios vacíos|`find /ruta -empty`|

#### Por tiempo

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-mtime`|Días desde la última modificación|`find /ruta -mtime -7` (últimos 7 días)|
|`-atime`|Días desde el último acceso|`find /ruta -atime +30` (más de 30 días sin acceso)|
|`-ctime`|Días desde el cambio de estado (inodo)|`find /ruta -ctime 0` (hoy)|
|`-mmin`|Minutos desde la última modificación|`find /ruta -mmin -60` (última hora)|
|`-amin`|Minutos desde el último acceso|`find /ruta -amin +120` (más de 2 horas sin acceso)|
|`-cmin`|Minutos desde el cambio de estado|`find /ruta -cmin 0` (justo ahora)|
|`-newer`|Archivos modificados después del archivo de referencia|`find /ruta -newer referencia.txt`|
|`-anewer`|Archivos accedidos después del archivo de referencia|`find /ruta -anewer referencia.txt`|

#### Por permisos y usuario

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-perm`|Busca por permisos específicos|`find /ruta -perm 644` (exactamente 644)|
|`-user`|Archivos pertenecientes a un usuario|`find /ruta -user juan`|
|`-group`|Archivos pertenecientes a un grupo|`find /ruta -group usuarios`|
|`-uid`|Busca por UID numérico|`find /ruta -uid 1000`|
|`-gid`|Busca por GID numérico|`find /ruta -gid 100`|
|`-nouser`|Archivos sin usuario propietario en /etc/passwd|`find /ruta -nouser`|
|`-nogroup`|Archivos sin grupo propietario en /etc/group|`find /ruta -nogroup`|

#### Por profundidad

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-maxdepth`|Profundidad máxima de búsqueda|`find /ruta -maxdepth 2 -name "*.txt"`|
|`-mindepth`|Profundidad mínima de búsqueda|`find /ruta -mindepth 3 -name "*.txt"`|

## Operadores lógicos

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-and` o `-a`|Operador AND (implícito)|`find /ruta -name "*.txt" -type f`|
|`-or` o `-o`|Operador OR|`find /ruta -name "*.txt" -o -name "*.pdf"`|
|`-not` o `!`|Operador NOT|`find /ruta -not -name "*.tmp"`|
|`(` `)`|Agrupación de expresiones|`find /ruta \( -name "*.txt" -o -name "*.pdf" \) -type f`|

## Acciones

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-print`|Imprime la ruta completa (predeterminado)|`find /ruta -name "*.txt" -print`|
|`-print0`|Imprime separado por null (para xargs -0)|`find /ruta -name "*.txt" -print0`|
|`-printf`|Formato de salida personalizado|`find /ruta -printf "%p - %s bytes\n"`|
|`-ls`|Lista en formato ls -dils|`find /ruta -name "*.txt" -ls`|
|`-exec`|Ejecuta comando sobre cada archivo|`find /ruta -name "*.txt" -exec rm {} \;`|
|`-execdir`|Ejecuta comando en el directorio del archivo|`find /ruta -name "*.txt" -execdir chmod 644 {} \;`|
|`-ok`|Como -exec pero pide confirmación|`find /ruta -name "*.txt" -ok rm {} \;`|
|`-delete`|Elimina los archivos encontrados|`find /ruta -name "*.tmp" -delete`|
|`-prune`|No desciende en directorios específicos|`find /ruta -name "node_modules" -prune -o -name "*.js" -print`|

## Opciones de ordenación y control

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-fprint`|Escribe resultados en archivo|`find /ruta -name "*.txt" -fprint resultados.txt`|
|`-fprint0`|Escribe resultados separados por null en archivo|`find /ruta -name "*.txt" -fprint0 resultados.txt`|
|`-fls`|Escribe formato ls en archivo|`find /ruta -name "*.txt" -fls listado.txt`|