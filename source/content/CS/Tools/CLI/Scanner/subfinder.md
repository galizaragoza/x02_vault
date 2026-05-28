## Configuración de Entrada (Input)

Define los objetivos sobre los cuales se realizará la enumeración de subdominios.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-d`, `-domain`|Especifica un dominio único para encontrar subdominios.|`subfinder -d example.com`|
|`-dL`, `-list`|Archivo que contiene una lista de dominios a procesar.|`subfinder -dL dominios.txt`|

---

## Configuración de Fuentes (Sources)

Permite gestionar y filtrar las fuentes de datos (pasivas) que utiliza la herramienta.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-s`, `-sources`|Utiliza solo fuentes específicas para la enumeración.|`subfinder -s virustotal,census`|
|`-as`, `-all-sources`|Utiliza todas las fuentes disponibles para la enumeración.|`subfinder -as`|
|`-es`, `-exclude-sources`|Excluye fuentes específicas de la búsqueda.|`subfinder -es shodan`|
|`-use-all-sources`|Fuerza el uso de todas las fuentes (lento pero exhaustivo).|`subfinder -use-all-sources`|

---

## Filtros de Resultado (Filters)

Controlan qué información se procesa y se muestra en los resultados finales.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-m`, `-match`|Muestra solo subdominios que coincidan con una cadena.|`subfinder -m "dev"`|
|`-f`, `-filter`|Filtra o elimina subdominios que contengan una cadena.|`subfinder -f "prod"`|
|`-nW`, `-no-wildcard`|Elimina subdominios detectados como wildcard (comodín).|`subfinder -nW`|

---

## Rendimiento y Optimización (Performance)

Ajustes técnicos para balancear la velocidad y el consumo de recursos.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-t`, `-threads`|Número de hilos concurrentes (por defecto 10).|`subfinder -t 50`|
|`-timeout`|Segundos a esperar antes de terminar la conexión (por defecto 30).|`subfinder -timeout 15`|
|`-max-time`|Tiempo máximo en minutos para esperar por los resultados.|`subfinder -max-time 10`|
|`-rate-limit`|Máximo de peticiones por segundo enviadas a las fuentes.|`subfinder -rate-limit 20`|

---

## Formateo de Salida (Output)

Opciones para la visualización y almacenamiento de los datos descubiertos.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-o`, `-output`|Archivo donde se guardarán los resultados.|`subfinder -o resultados.txt`|
|`-oJ`, `-json`|Escribe la salida en formato JSON.|`subfinder -oJ`|
|`-oD`, `-output-dir`|Directorio para guardar resultados (si se procesa una lista).|`subfinder -dL list.txt -oD ./out`|
|`-cs`, `-collect-sources`|Incluye el nombre de la fuente en la salida.|`subfinder -cs`|
|`-oI`, `-ip`|Incluye la dirección IP del subdominio en la salida.|`subfinder -oI`|
|`-silent`|Muestra únicamente los subdominios encontrados (sin banners).|`subfinder -silent`|
|`-no-color`|Desactiva los colores en la salida de terminal.|`subfinder -no-color`|

---

## Configuración y API (Configuration)

Gestión de credenciales y archivos de configuración para las fuentes que requieren autenticación.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-pc`, `-config`|Ruta al archivo de configuración de subfinder.|`subfinder -pc config.yaml`|
|`-ls`, `-list-sources`|Lista todas las fuentes disponibles y su estado (con/sin API).|`subfinder -ls`|
|`-set-config`|Establece un valor de configuración específico.|`subfinder -set-config key=value`|

---

## Depuración y Actualización (Update/Debug)

Opciones para el mantenimiento y diagnóstico de la herramienta.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-v`, `-verbose`|Muestra información detallada del proceso y errores.|`subfinder -v`|
|`-version`|Muestra la versión instalada de subfinder.|`subfinder -version`|
|`-up`, `-update`|Actualiza la herramienta a la última versión disponible.|`subfinder -up`|
|`-duc`, `-disable-update-check`|Desactiva la comprobación automática de actualizaciones.|`subfinder -duc`|