Curl envía  y recibe información mediante distintos protocolos (HTTP, HTTPS, FTP...) y métodos (GET, POST, PUT, DELETE...), además, nos permite manipular solicitudes modificando cabeceras, cookies...

``` bash
curl [opciones] <URL>
```
### Control de Protocolos y Conectividad

Esta sección abarca las opciones que definen cómo `curl` interactúa con la capa de red y los protocolos de transporte.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-0`, `--http1.0`|Fuerza el uso de HTTP versión 1.0.|`curl --http1.0 [http://example.com](http://example.com)`|
|`--http1.1`|Fuerza el uso de HTTP versión 1.1.|`curl --http1.1 [http://example.com](http://example.com)`|
|`--http2`|Habilita el uso de HTTP/2 si el servidor lo soporta.|`curl --http2 [https://example.com](https://example.com)`|
|`--http3`|Habilita el uso de HTTP/3 (QUIC).|`curl --http3 [https://example.com](https://example.com)`|
|`-4`, `--ipv4`|Resuelve nombres de host solo a direcciones IPv4.|`curl -4 [http://example.com](http://example.com)`|
|`-6`, `--ipv6`|Resuelve nombres de host solo a direcciones IPv6.|`curl -6 [http://example.com](http://example.com)`|
|`-x`, `--proxy`|Utiliza un proxy para la conexión.|`curl -x proxy.net:8080 [http://site.com](http://site.com)`|
|`--socks5`|Especifica el uso de un proxy SOCKS5.|`curl --socks5 127.0.0.1:9050 [http://site.com](http://site.com)`|

### Gestión de Cabeceras (Headers) y Métodos HTTP

Configuración de metadatos de la petición y definición del verbo HTTP a emplear.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-A`, `--user-agent`|Define la cadena de User-Agent enviada al servidor.|`curl -A "Mozilla/5.0" [http://example.com](http://example.com)`|
|`-e`, `--referer`|Especifica la URL de procedencia (Referer).|`curl -e "[http://google.com](http://google.com)" [http://site.com](http://site.com)`|
|`-H`, `--header`|Añade o modifica una cabecera HTTP personalizada.|`curl -H "X-MyHeader: Value" [http://api.com](http://api.com)`|
|`-I`, `--head`|Realiza una petición HEAD para obtener solo las cabeceras.|`curl -I [http://example.com](http://example.com)`|
|`-X`, `--request`|Especifica el método de petición (GET, POST, PUT, DELETE).|`curl -X DELETE [http://api.com/user/1](http://api.com/user/1)`|
|`--compressed`|Solicita una respuesta comprimida (gzip, deflate, etc.).|`curl --compressed [http://example.com](http://example.com)`|

### Envío y Transferencia de Datos

Opciones relativas al cuerpo de la petición (payload) y la subida de archivos.

| Opción                | Descripción                                                           | Ejemplo                                                                          |
| --------------------- | --------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| `-d`, `--data`        | Envía datos en una petición POST (application/x-www-form-urlencoded). | `curl -d "name=user&pass=123" [http://site.com](http://site.com)`                |
| `--data-raw`          | Envía datos POST sin procesar el símbolo `@`.                         | `curl --data-raw "{'id':1}" [http://api.com](http://api.com)`                    |
| `--data-binary`       | Envía datos POST exactamente como se especifican, sin procesar.       | `curl --data-binary @file.bin [http://api.com](http://api.com)`                  |
| `-F`, `--form`        | Envía datos como multipart/form-data (emulación de formulario).       | `curl -F "file=@img.png" [http://upload.com](http://upload.com)`                 |
| `-T`, `--upload-file` | Transfiere un archivo local al destino (PUT o FTP).                   | `curl -T local.txt ftp://[server.com/remote.txt](https://server.com/remote.txt)` |
| `-G`, `--get`         | Convierte los datos de `-d` en parámetros de query string.            | `curl -G -d "search=curl" [http://google.com](http://google.com)`                |
### Formateo de Output y Registro

Control de la salida estándar, guardado de archivos y depuración.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-o`, `--output`|Escribe la respuesta en un archivo en lugar de stdout.|`curl -o result.html [http://example.com](http://example.com)`|
|`-O`, `--remote-name`|Guarda el archivo usando el nombre remoto.|`curl -O [http://example.com/file.zip](http://example.com/file.zip)`|
|`-s`, `--silent`|Modo silencioso. No muestra progreso ni errores.|`curl -s [http://example.com](http://example.com)`|
|`-S`, `--show-error`|En combinación con `-s`, muestra errores si fallan.|`curl -sS [http://example.com](http://example.com)`|
|`-v`, `--verbose`|Muestra información detallada de la transacción.|`curl -v [http://example.com](http://example.com)`|
|`--trace`|Vuelca un seguimiento detallado en un archivo.|`curl --trace debug.txt [http://example.com](http://example.com)`|
|`-w`, `--write-out`|Define un formato para mostrar variables tras la ejecución.|`curl -w "%{http_code}\n" [http://api.com](http://api.com)`|

### Seguridad y Autenticación

Configuración de certificados SSL/TLS y credenciales de acceso.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-u`, `--user`|Proporciona credenciales para autenticación básica.|`curl -u user:password [http://api.com](http://api.com)`|
|`-k`, `--insecure`|Permite conexiones SSL inseguras (ignora certificados).|`curl -k [https://expired-cert.com](https://expired-cert.com)`|
|`-E`, `--cert`|Especifica el archivo de certificado de cliente (PEM).|`curl --cert client.pem [https://secure.com](https://secure.com)`|
|`--cacert`|Especifica un archivo CA para verificar el peer.|`curl --cacert ca.pem [https://secure.com](https://secure.com)`|
|`--key`|Proporciona la clave privada para el certificado.|`curl --key key.pem [https://secure.com](https://secure.com)`|
|`--tlsv1.2`|Fuerza el uso de TLS versión 1.2 o superior.|`curl --tlsv1.2 [https://example.com](https://example.com)`|

### Rendimiento y Evasión

Opciones para limitar el impacto en red y ajustar el comportamiento de la petición.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-L`, `--location`|Sigue redirecciones (cabecera Location).|`curl -L [http://google.com](http://google.com)`|
|`--max-redirs`|Limita el número máximo de redirecciones a seguir.|`curl -L --max-redirs 5 [http://site.com](http://site.com)`|
|`--limit-rate`|Limita la velocidad máxima de transferencia.|`curl --limit-rate 100k [http://file.com](http://file.com)`|
|`-m`, `--max-time`|Tiempo máximo permitido para toda la operación.|`curl -m 10 [http://slow.com](http://slow.com)`|
|`--connect-timeout`|Tiempo máximo para establecer la conexión inicial.|`curl --connect-timeout 5 [http://site.com](http://site.com)`|
|`--retry`|Número de reintentos en caso de error transitorio.|`curl --retry 3 [http://unstable.com](http://unstable.com)`|
|`-b`, `--cookie`|Envía cookies al servidor desde un string o archivo.|`curl -b "session=123" [http://site.com](http://site.com)`|
|`-c`, `--cookie-jar`|Guarda las cookies recibidas en un archivo.|`curl -c cookies.txt [http://site.com](http://site.com)`|

### Gestión de Resolución de Nombres

Configuración avanzada de DNS y resolución de hosts.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`--resolve`|Fuerza una IP específica para un host y puerto.|`curl --resolve example.com:443:127.0.0.1 [https://example.com](https://example.com)`|
|`--dns-servers`|Especifica servidores DNS alternativos.|`curl --dns-servers 8.8.8.8 [http://site.com](http://site.com)`|
|`--noproxy`|Lista de hosts que no deben usar el proxy.|`curl --noproxy "localhost,example.com" [http://site.com](http://site.com)`|
# Protips

### Checkear IP pública
```
curl ifconfig.me
```
### Fecha exacta (en GMT)
```sh
curl -sI google.com | grep -w "Date:" | cut -d' ' -f2-
```