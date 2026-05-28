## gowitness

gowitness es una utilidad escrita en Go que utiliza Chrome Headless para generar capturas de pantalla de servicios web. Se destaca por su alta eficiencia, el uso de una base de datos SQLite para el almacenamiento de resultados y sus capacidades de visualización mediante una interfaz web integrada.

### Subherramienta: `scan` (Captura de objetivos)

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-f`, `--file`|Especifica el archivo con la lista de objetivos.|`gowitness scan file -f targets.txt`|
|`-u`, `--url`|Captura una URL individual.|`gowitness scan single -u [https://example.com](https://example.com)`|
|`--cidr`|Escanea un rango de red completo en formato CIDR.|`gowitness scan cidr --cidr 192.168.1.0/24`|
|`--nmap`|Importa y escanea desde un archivo XML de Nmap.|`gowitness scan nmap -f scan.xml`|
|`--threads`|Define el número de procesos paralelos.|`gowitness scan file -f targets.txt --threads 20`|


### Subherramienta: `server` (Visualización de resultados)

|Opción|Descripción|Ejemplo|
|---|---|---|
|`--address`|Dirección de escucha para la interfaz web del reporte.|`gowitness server --address 0.0.0.0`|
|`--port`|Puerto de escucha para la interfaz web.|`gowitness server --port 8081`|
|`--db`|Especifica la ruta a la base de datos SQLite personalizada.|`gowitness server --db results.db`|


### Configuración de Renderizado y Evasión

|Opción|Descripción|Ejemplo|
|---|---|---|
|`--resolution-x`|Define el ancho de la captura de pantalla.|`gowitness scan single -u url.com --resolution-x 1920`|
|`--resolution-y`|Define el alto de la captura de pantalla.|`gowitness scan single -u url.com --resolution-y 1080`|
|`--delay`|Segundos a esperar tras la carga de la página antes de capturar.|`gowitness scan file -f t.txt --delay 5`|
|`--user-agent`|Establece el User-Agent para el navegador headless.|`gowitness scan file --user-agent "Mozilla/5.0"`|
|`--proxy`|URL del proxy para las peticiones del navegador.|`gowitness scan file --proxy [http://127.0.0.1:8080](http://127.0.0.1:8080)`|