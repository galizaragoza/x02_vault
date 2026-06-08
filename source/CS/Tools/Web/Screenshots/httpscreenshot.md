## httpscreenshot

httpscreenshot es una herramienta basada en Python y PhantomJS (o Selenium) diseñada para realizar capturas de pantalla de un gran número de sitios web y recolectar las respuestas HTTP, optimizada para su uso con resultados de escaneos masivos.

### Parámetros de Entrada y Red

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-i`, `--input-file`|Archivo de entrada con líneas en formato `IP:PORT` o URLs.|`httpscreenshot -i targets.txt`|
|`-p`, `--port`|Puerto por defecto si no se especifica en el archivo de entrada.|`httpscreenshot -i ips.txt -p 443`|
|`-w`, `--workers`|Número de subprocesos (workers) para el escaneo.|`httpscreenshot -i targets.txt -w 15`|
|`-t`, `--timeout`|Tiempo de espera para la conexión y carga.|`httpscreenshot -i targets.txt -t 20`|


### Control de Captura y Output

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-o`, `--output-directory`|Directorio donde se almacenarán las imágenes y logs.|`httpscreenshot -i targets.txt -o ./capturas`|
|`-v`, `--verbose`|Muestra información detallada durante la ejecución.|`httpscreenshot -i targets.txt -v`|
|`--ssl`|Fuerza el uso de HTTPS para todos los objetivos.|`httpscreenshot -i targets.txt --ssl`|
|`-u`, `--url`|Procesa una única URL específica.|`httpscreenshot -u [http://example.com](http://example.com)`|


### Evasión y Autenticación

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-a`, `--user-agent`|Especifica el User-Agent para las peticiones.|`httpscreenshot -i targets.txt -a "Googlebot"`|
|`-c`, `--cookie`|Añade una cookie específica a las peticiones de captura.|`httpscreenshot -i targets.txt -c "session=id123"`|
|`-x`, `--proxy`|Configura un proxy HTTP para las capturas.|`httpscreenshot -i targets.txt -x 127.0.0.1:8080`|
