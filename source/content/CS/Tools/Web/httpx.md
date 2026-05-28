## Configuración de Entrada (Input)
Esta sección define cómo la herramienta procesa los objetivos y gestiona la lectura de datos.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-l`, `-list`|Archivo que contiene la lista de hosts a procesar.|`httpx -l urls.txt`|
|`-u`, `-target`|Define un objetivo único directamente en la CLI.|`httpx -u [https://example.com](https://example.com)`|
|`-request`|Archivo que contiene una petición HTTP en formato raw.|`httpx -request req.txt`|

## Filtros de Sondeo (Probing)
Controlan qué tipo de peticiones se realizan y cómo se validan los servicios.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-p`, `-ports`|Puertos específicos a sondear (soporta rangos).|`httpx -p 80,443,8080`|
|`-path`|Ruta específica para consultar en el host.|`httpx -path /api/v1/health`|
|`-no-fallback`|Deshabilita el intento de HTTP si HTTPS falla.|`httpx -u example.com -no-fallback`|
|`-no-fallback-scheme`|No realiza fallback de esquema si el protocolo no está definido.|`httpx -l list.txt -nfs`|
|`-method`|Método HTTP a utilizar en la petición.|`httpx -method PUT`|
|`-body`|Cuerpo de la petición HTTP.|`httpx -body "{\"id\":1}"`|

## Filtrado de Resultados (Matchers & Filters)
Permiten incluir o excluir resultados basados en las respuestas del servidor.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-mc`, `-match-code`|Incluye solo respuestas con códigos de estado específicos.|`httpx -mc 200,302`|
|`-fc`, `-filter-code`|Excluye respuestas con códigos de estado específicos.|`httpx -fc 404,403`|
|`-ml`, `-match-length`|Incluye respuestas con longitudes de cuerpo específicas.|`httpx -ml 1245`|
|`-fl`, `-filter-length`|Excluye respuestas con longitudes de cuerpo específicas.|`httpx -fl 0`|
|`-mrt`, `-match-rt`|Incluye respuestas con tiempos de respuesta específicos.|`httpx -mrt "< 100ms"`|
|`-ms`, `-match-string`|Incluye respuestas que contengan una cadena específica.|`httpx -ms "Admin Panel"`|
|`-er`, `-extract-regex`|Extrae contenido basado en una expresión regular.|`httpx -er "id=[0-9]+"`|

## Extracción de Metadatos (Output Data)
Define qué información adicional se debe mostrar u obtener de las cabeceras y el cuerpo.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-td`, `-tech-detect`|Detecta tecnologías basadas en `wappalyzer`.|`httpx -td`|
|`-title`|Extrae y muestra el título de la página HTML.|`httpx -title`|
|`-server`|Muestra el valor de la cabecera "Server".|`httpx -server`|
|`-ip`|Muestra la dirección IP del host.|`httpx -ip`|
|`-cname`|Muestra los registros CNAME del host.|`httpx -cname`|
|`-asn`|Muestra información del ASN.|`httpx -asn`|
|`-location`|Muestra la URL de redirección (cabecera Location).|`httpx -location`|
|`-hash`|Calcula el hash del cuerpo de la respuesta (sha256, mmh3, etc.).|`httpx -hash sha256`|
|`-tls-probe`|Extrae información del certificado TLS.|`httpx -tls-probe`|
|`-csp-probe`|Extrae dominios de la Content-Security-Policy.|`httpx -csp-probe`|

## Configuración de Salida (Output Formatting)
Opciones para el almacenamiento y la visualización de los datos procesados.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-o`, `-output`|Archivo donde se guardarán los resultados.|`httpx -o results.txt`|
|`-json`|Formatea la salida en formato JSON.|`httpx -json`|
|`-csv`|Formatea la salida en formato CSV.|`httpx -csv`|
|`-sr`, `-store-response`|Guarda la respuesta HTTP en un directorio dedicado.|`httpx -sr -srd responses/`|
|`-silent`|Muestra solo los resultados finales en la terminal.|`httpx -silent`|
|`-no-color`|Desactiva los colores en la salida de terminal.|`httpx -no-color`|

## Rendimiento y Optimización (Performance)
Ajustes para controlar la velocidad y el consumo de recursos.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-t`, `-threads`|Número de hilos concurrentes (por defecto 50).|`httpx -t 100`|
|`-rl`, `-rate-limit`|Máximo de peticiones por segundo.|`httpx -rl 20`|
|`-timeout`|Tiempo de espera máximo para una conexión.|`httpx -timeout 10s`|
|`-retries`|Número de reintentos en caso de fallo.|`httpx -retries 3`|

## Evasión y Red (Network & Evasion)
Opciones para gestionar proxies, cabeceras personalizadas y comportamiento de red.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-H`, `-header`|Añade cabeceras HTTP personalizadas.|`httpx -H "User-Agent: bot"`|
|`-proxy`|Define un proxy HTTP/SOCKS5.|`httpx -proxy [http://127.0.0.1:8080](http://127.0.0.1:8080)`|
|`-vhost`|Habilita el sondeo de VHOSTs.|`httpx -vhost`|
|`-follow-redirects`|Sigue las redirecciones HTTP.|`httpx -fr`|
|`-max-redirects`|Límite máximo de redirecciones a seguir.|`httpx -max-redirects 5`|
|`-http-proxy`|Proxy específico para tráfico HTTP.|`httpx -http-proxy http://proxy:3128`|
|`-unsafe`|Utiliza una librería HTTP menos estricta (útil para payloads).|`httpx -unsafe`|

## Depuración y Actualización (Debug)

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-v`, `-verbose`|Muestra información detallada de los errores.|`httpx -v`|
|`-version`|Muestra la versión actual de la herramienta.|`httpx -version`|
|`-up`, `-update`|Actualiza la herramienta a la última versión estable.|`httpx -up`|
|`-stats`|Muestra estadísticas en tiempo real durante la ejecución.|`httpx -stats`|