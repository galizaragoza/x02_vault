

|**Utilidad / Comando**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`init`|Inicializa la CLI con tu API Key de Shodan.|`shodan init TU_API_KEY`|
|`count`|Devuelve el número total de resultados para una consulta.|`shodan count "apache 2.4"`|
|`search`|Busca en la base de datos y muestra los resultados en la terminal.|`shodan search --fields ip_str,port product:nginx`|
|`host`|Muestra información detallada sobre una IP específica (puertos, vulnerabilidades).|`shodan host 8.8.8.8`|
|`myip`|Devuelve tu dirección IP pública vista desde Shodan.|`shodan myip`|
|`domain`|Provee una vista general de los subdominios y registros DNS de un dominio.|`shodan domain google.com`|
|`download`|Descarga los resultados de una búsqueda en un archivo comprimido JSON.|`shodan download resultados_camaras "webcam"`|
|`parse`|Extrae información específica de un archivo descargado con `download`.|`shodan parse --fields ip_str resultados.json.gz`|