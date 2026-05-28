## strace

`strace` es una utilidad de diagnóstico, instrucción y depuración para Linux que permite interceptar y registrar las llamadas al sistema (`syscalls`) ejecutadas por un proceso y las señales que recibe. Es una herramienta esencial para analizar fallos de ejecución donde el código fuente no está disponible o para entender cómo un binario interactúa con el kernel. Muestra los argumentos pasados a cada llamada y el valor de retorno en tiempo real.

### Control de Ejecución y Proceso
Opciones para definir qué proceso se desea monitorizar.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-p`|Se adjunta a un proceso en ejecución mediante su PID.|`strace -p 1234`|
|`-f`|Rastrea los procesos hijos creados por el proceso principal (forks).|`strace -f ./binario`|
|`-u`|Ejecuta el comando bajo el ID de usuario especificado.|`strace -u username ls`|
|`-D`|Ejecuta el proceso rastreado como un proceso nieto (desacoplado).|`strace -D ./server`|


### Filtrado de Información
Permite reducir el ruido capturando solo eventos específicos.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-e`|Filtra por una expresión específica (llamada, red, archivos, etc.).|`strace -e trace=open,read ./bin`|
|`-e trace=file`|Rastrea todas las llamadas al sistema relacionadas con archivos.|`strace -e trace=file ls`|
|`-e trace=network`|Rastrea todas las llamadas al sistema relacionadas con red.|`strace -e trace=network curl google.com`|
|`-v`|Muestra versiones detalladas (no abreviadas) de estructuras de datos.|`strace -v ls`|


### Formateo y Estadísticas de Salida
Opciones para modificar la presentación de los datos recolectados.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-o`|Redirige la salida de `strace` a un archivo de texto.|`strace -o log.txt ./bin`|
|`-c`|Genera un reporte estadístico al finalizar (tiempo, llamadas, errores).|`strace -c ls`|
|`-t`|Prefija cada línea con la hora del día.|`strace -t ls`|
|`-T`|Muestra el tiempo transcurrido dentro de cada llamada al sistema.|`strace -T ls`|
|`-s`|Especifica el tamaño máximo de cadena a imprimir (por defecto 32).|`strace -s 1024 ./bin`|