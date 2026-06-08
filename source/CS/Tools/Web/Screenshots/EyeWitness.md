EyeWitness es una herramienta diseñada para tomar capturas de pantalla de sitios web, proporcionar información de cabeceras del servidor e identificar credenciales por defecto si es posible. Es ampliamente utilizada en auditorías de seguridad para clasificar rápidamente la superficie de ataque web y detectar servicios expuestos.

### Selección de Objetivos y Protocolos

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-f`, `--file`|Archivo local que contiene las URLs u objetivos a analizar.|`eyewitness -f targets.txt`|
|`-x`, `--xml`|Importa resultados de un escaneo de Nmap en formato XML.|`eyewitness -x nmap_scan.xml`|
|`--single`|Especifica una única URL para capturar.|`eyewitness --single [http://example.com](http://example.com)`|
|`--web`|Fuerza el análisis de servicios HTTP/HTTPS.|`eyewitness --web -f targets.txt`|
|`--rdp`|Captura de pantalla de servicios RDP (Remote Desktop Protocol).|`eyewitness --rdp -f hosts.txt`|
|`--vnc`|Captura de pantalla de servicios VNC.|`eyewitness --vnc -f hosts.txt`|


### Configuración del Escaneo y Rendimiento

|Opción|Descripción|Ejemplo|
|---|---|---|
|`--threads`|Número de hilos simultáneos para el procesamiento.|`eyewitness --threads 10 -f targets.txt`|
|`--timeout`|Tiempo de espera en segundos para la carga de una página.|`eyewitness --timeout 30 -f targets.txt`|
|`--user-agent`|Define un User-Agent personalizado para las peticiones.|`eyewitness --user-agent "CustomAgent 1.0"`|
|`--proxy-ip`|Dirección IP del proxy para enrutar el tráfico.|`eyewitness --proxy-ip 127.0.0.1`|
|`--proxy-port`|Puerto del proxy especificado.|`eyewitness --proxy-port 8080`|
|`--proxy-type`|Tipo de proxy (HTTP, SOCKS5).|`eyewitness --proxy-type socks5`|


### Salida y Reporte

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-d`, `--directory`|Directorio de salida para los informes y capturas.|`eyewitness -d ./reporte_auditoria`|
|`--no-prompt`|Ejecuta la herramienta sin solicitar confirmaciones al usuario.|`eyewitness -f targets.txt --no-prompt`|
|`--results`|Número de resultados por página en el informe HTML.|`eyewitness --results 25`|
|`--no-dns`|Omite la resolución DNS para acelerar el proceso.|`eyewitness --no-dns -f targets.txt`|