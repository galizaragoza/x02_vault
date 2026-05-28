`trace-cmd` es una interfaz de línea de comandos diseñada para interactuar con el subsistema **Ftrace** del kernel de Linux. Su propósito principal es facilitar la captura, manipulación y análisis de trazas de eventos del sistema, permitiendo a los administradores y desarrolladores diagnosticar problemas de rendimiento, latencia y comportamiento del kernel. La herramienta actúa como un front-end avanzado que abstrae la complejidad de interactuar directamente con el sistema de archivos `debugfs` o `tracefs`.
## trace-cmd record
Esta subherramienta se utiliza para capturar trazas y escribirlas en un archivo de datos (por defecto `trace.dat`). Es la función principal para la recolección de métricas en tiempo de ejecución.
### Opciones de Configuración de Rastreo

|Flag|Descripción|Ejemplo Práctico|
|---|---|---|
|`-p <tracer>`|Especifica el plugin de rastreo (e.g., `function`, `function_graph`, `blk`).|`trace-cmd record -p function`|
|`-e <event>`|Habilita un evento específico del kernel.|`trace-cmd record -e sched_switch`|
|`-g <function>`|Para el plugin `function_graph`, especifica la función sobre la que se generará el grafo.|`trace-cmd record -p function_graph -g sys_read`|
|`-f <filter>`|Aplica un filtro de eventos. Debe seguir al parámetro `-e`.|`trace-cmd record -e 'sys_enter' -f 'nr==1'`|
|`-R <trigger>`|Configura un disparador (trigger) para un evento específico.|`trace-cmd record -e 'sched_switch' -R 'stacktrace'`|
|`-v`|Invierte el filtrado de eventos (excluye los eventos especificados).|`trace-cmd record -v -e irq`|

### Opciones de Control de Ejecución y Proceso

|Flag|Descripción|Ejemplo Práctico|
|---|---|---|
|`-P <pid>`|Rastra solo los eventos asociados a un ID de proceso específico.|`trace-cmd record -P 1234`|
|`-c`|Rastrea también los procesos hijos del comando ejecutado.|`trace-cmd record -c -e syscalls ls`|
|`-F`|Rastrea solo el ejecutable proporcionado en la línea de comandos.|`trace-cmd record -F my_app`|
|`-u`|Utiliza el rastreo de eventos en espacio de usuario (si está soportado).|`trace-cmd record -u -e my_uprobe`|


### Opciones de Salida y Rendimiento

|Flag|Descripción|Ejemplo Práctico|
|---|---|---|
|`-o <file>`|Especifica el nombre del archivo de salida.|`trace-cmd record -o output.dat`|
|`-b <size>`|Define el tamaño del búfer de anillo (ring buffer) en kilobytes por CPU.|`trace-cmd record -b 10240`|
|`-B <name>`|Crea una instancia de búfer separada con el nombre especificado.|`trace-cmd record -B user_buffer -e sched`|
|`-m <size>`|Define el tamaño máximo del archivo de salida en kilobytes.|`trace-cmd record -m 50000`|
|`-r <priority>`|Establece la prioridad en tiempo real del hilo de grabación.|`trace-cmd record -r 99`|
|`-s <interval>`|Define el intervalo de tiempo (en microsegundos) para el muestreo.|`trace-cmd record -s 100`|


## trace-cmd report
Se encarga de leer los archivos generados por `record` y presentar la información en un formato legible para el usuario.
### Opciones de Formateo y Filtrado

|Flag|Descripción|Ejemplo Práctico|
|---|---|---|
|`-i <file>`|Especifica el archivo de entrada (por defecto `trace.dat`).|`trace-cmd report -i custom.dat`|
|`-f`|Muestra solo las funciones registradas en la traza.|`trace-cmd report -f`|
|`-l`|Muestra los latidos del reloj (latencia) con mayor detalle.|`trace-cmd report -l`|
|`-E`|Muestra los eventos de error registrados.|`trace-cmd report -E`|
|`-P`|Muestra los eventos filtrados por ID de proceso en el reporte.|`trace-cmd report -P 1234`|
|`-v`|Modo detallado (verbose).|`trace-cmd report -v`|
|`-T`|Muestra las marcas de tiempo de los eventos.|`trace-cmd report -T`|


### Opciones de Análisis de Datos

|Flag|Descripción|Ejemplo Práctico|
|---|---|---|
|`--stat`|Proporciona estadísticas sobre los eventos contenidos en el archivo.|`trace-cmd report --stat`|
|`-O <option>`|Pasa opciones específicas al manejador de eventos del reporte.|`trace-cmd report -O offset`|
|`-L`|No carga los plugins de eventos locales.|`trace-cmd report -L`|


## trace-cmd list
Permite consultar las capacidades actuales del sistema de rastreo del kernel.

|Flag|Descripción|Ejemplo Práctico|
|---|---|---|
|`-e`|Lista todos los eventos disponibles que el kernel puede rastrear.|`trace-cmd list -e`|
|`-p`|Lista los plugins de rastreo (tracers) disponibles.|`trace-cmd list -p`|
|`-f`|Lista los nombres de las funciones que pueden ser rastreadas.|`trace-cmd list -f`|
|`-v`|Lista los eventos con sus formatos y campos de filtro.|`trace-cmd list -v`|
|`-t`|Lista los tipos de reloj disponibles para el rastreo.|`trace-cmd list -t`|


## Gestión de Estado (stop, clear, reset, extract)
Estas subherramientas controlan el estado del grabador de Ftrace sin necesidad de reiniciar la configuración completa.

|Subherramienta|Descripción|Ejemplo Práctico|
|---|---|---|
|`stop`|Detiene la escritura en el búfer de anillo, pero mantiene la configuración.|`trace-cmd stop`|
|`clear`|Limpia el contenido actual del búfer de anillo.|`trace-cmd clear`|
|`reset`|Deshabilita todo el rastreo y restaura la configuración por defecto.|`trace-cmd reset`|
|`extract`|Extrae los datos actuales del búfer del kernel y los guarda en un archivo.|`trace-cmd extract -o data.dat`|


## Diagnóstico de Sistema (stat, stack, snapshot)
Herramientas para inspeccionar el estado actual de las estructuras de rastreo.

|Subherramienta / Flag|Descripción|Ejemplo Práctico|
|---|---|---|
|`stat`|Muestra el estado actual del sistema de rastreo (eventos activos, buffers, etc.).|`trace-cmd stat`|
|`stack`|Habilita o muestra el rastreo de la pila de llamadas (stack trace).|`trace-cmd stack`|
|`snapshot`|Crea una captura instantánea del búfer actual sin detener el rastreo.|`trace-cmd snapshot -s`|


## Operaciones de Red y Avanzadas (listen, split, restore)

|Subherramienta|Descripción|Ejemplo Práctico|
|---|---|---|
|`listen -p <port>`|Inicia un servidor para recibir trazas de nodos remotos a través de la red.|`trace-cmd listen -p 8080`|
|`split`|Divide un archivo `trace.dat` grande en múltiples archivos más pequeños.|`trace-cmd split -i trace.dat 100M`|
|`restore`|Intenta restaurar un archivo de traza dañado o incompleto.|`trace-cmd restore -i broken.dat`|
|`check-events`|Verifica que todos los formatos de eventos en el sistema sean parseables.|`trace-cmd check-events`|