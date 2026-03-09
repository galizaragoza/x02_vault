# Google
|**Operador**|**Descripción**|**Ejemplo de Sintaxis**|
|---|---|---|
|`site:`|Igual que en Google, limita al dominio o subdominio.|`site:gob.es`|
|`contains:`|Busca páginas que contengan enlaces a un tipo de archivo.|`contains:pdf "manual"`|
|`filetype:`|Similar a Google, filtra por tipo de archivo.|`filetype:xls "lista de usuarios"`|
|`intitle:`|Busca términos en el título de la página.|`intitle:"Index of /"`|
|`ip:`|**(Exclusivo)** Busca sitios alojados en una dirección IP específica.|`ip:192.168.1.1`|
|`feed:`|Busca feeds RSS o Atom asociados a un sitio.|`feed:cnn.com`|
|`url:`|Verifica si una URL específica está indexada.|`url:ejemplo.com/archivo.txt`|
|`link:`|Busca páginas que enlazan a la URL especificada.|`link:ejemplo.com`|#

# Bing
|**Operador**|**Descripción**|**Ejemplo de Sintaxis**|
|---|---|---|
|`site:`|Igual que en Google, limita al dominio o subdominio.|`site:gob.es`|
|`contains:`|Busca páginas que contengan enlaces a un tipo de archivo.|`contains:pdf "manual"`|
|`filetype:`|Similar a Google, filtra por tipo de archivo.|`filetype:xls "lista de usuarios"`|
|`intitle:`|Busca términos en el título de la página.|`intitle:"Index of /"`|
|`ip:`|**(Exclusivo)** Busca sitios alojados en una dirección IP específica.|`ip:192.168.1.1`|
|`feed:`|Busca feeds RSS o Atom asociados a un sitio.|`feed:cnn.com`|
|`url:`|Verifica si una URL específica está indexada.|`url:ejemplo.com/archivo.txt`|
|`link:`|Busca páginas que enlazan a la URL especificada.|`link:ejemplo.com`|#
# Shodan
|**Operador**|**Descripción**|**Ejemplo de Sintaxis**|
|---|---|---|
|`product:`|Busca un software o marca específica.|`product:"Apache httpd"`|
|`port:`|Filtra dispositivos que tengan un puerto específico abierto.|`port:3389` (RDP)|
|`net:`|Limita la búsqueda a un rango de IP o subred (CIDR).|`net:192.168.1.0/24`|
|`hostname:`|Busca dispositivos asociados a un nombre de host o dominio.|`hostname:google.com`|
|`os:`|Filtra por el sistema operativo detectado.|`os:"Windows 7"`|
|`city:`|Limita los resultados a una ciudad específica.|`city:"Madrid"`|
|`country:`|Limita por código de país (ISO 3166-1 alpha-2).|`country:ES`|
|`org:`|Busca dispositivos que pertenecen a una organización/ISP.|`org:"Telefonica"`|
|`vuln:`|**(Requiere cuenta pago)** Busca IPs con una CVE específica.|`vuln:CVE-2017-0144`|
|`screenshot.label:`|Busca por etiquetas en capturas de pantalla (ej. "webcam").|`screenshot.label:ics`|
# GitHub
|**Operador**|**Descripción**|**Ejemplo de Sintaxis**|
|---|---|---|
|`extension:`|Filtra por extensión de archivo (ideal para configs).|`extension:env` o `extension:pem`|
|`filename:`|Busca un nombre de archivo exacto.|`filename:config.php`|
|`path:`|Busca dentro de una ruta o carpeta específica.|`path:etc/shadow`|
|`user:`|Limita la búsqueda a un usuario o repositorio.|`user:nombre_usuario password`|
|`org:`|Busca dentro de todos los repositorios de una organización.|`org:microsoft secret`|
|`language:`|Filtra por el lenguaje de programación del archivo.|`language:python "api_key"`|
|`size:`|Busca archivos de un tamaño determinado (en bytes).|`size:>10000`|
|`NOT`|Excluye términos (debe ir en mayúsculas).|`password NOT example`|
|`" "`|Busca una cadena exacta (tokens o mensajes de error).|`"BEGIN RSA PRIVATE KEY"`|
