``` bash
nmap -sV -p 1-65535 192.168.1.1
```
# Parámetros

|                                        |                                                                                                           |                                   |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------- | --------------------------------- |
| ``-p-``                                | escanea todos los 65535 puertos, mejor usar `-p 0-65535`                                                  | nmap -p- <IP víctima>             |
| ``--open``                             | muestra solo los puertos abiertos                                                                         | nmap --open <IP víctima>          |
| ``-T0  a -T5``                         | controla la agresividad del escaneo, siendo 0 discreto y lento y 5 agresivo e intrusivo                   | nmap -T3 <IP víctima>             |
| ``--min-rate=<número>``                | establece los paquetes por segundo                                                                        | nmap --min-rate=5000 <IP víctima> |
| ``-sC``                                | lanza comprobaciones adicionales en los puertos abiertos para identificar los servicios siendo ejecutados | nmap -sC <IP víctima>             |
| ``-sV``                                | detecta las versiones de los servicios                                                                    | nmap -sV <IP víctima>             |
| ``-O``                                 | detecta el sistema operativo                                                                              | nmap -O <IP víctima>              |
| ``--traceroute``                       | realiza un seguimiento de la ruta que toma un paquete de datos desde el host                              | nmap --traceroute <IP víctima>    |
| ``-A``                                 | junta en un solo parámetro -sV, -sC, -O y --traceroute                                                    | nmap -A <IP víctima>              |
| ``-sT``                                | realiza un escaneo TCP completo con 3-way handshake                                                       | nmap -sT <IP víctima>             |
| ``-sS``                                | realiza un escaneo TCP completo sin 3-way handshake                                                       | nmap -sS <IP víctima>             |
| `-sA`                                  | ACK scan                                                                                                  |                                   |
| ``-sU``                                | realiza un escaneo UDP completo                                                                           | nmap -sU <IP víctima>             |
| ``-sY``                                | realiza un escaneo SCTP                                                                                   | nmap -sY<IP víctima>              |
| ``-Pn``                                | asume que el host está activo y omite el ping                                                             | nmap -Pn <IP víctima>             |
| ``-n``                                 | deshabilita la traducción DNS                                                                             | nmap -n <IP víctima>              |
| ``-v``                                 | (verbose) muestra detalles del escaneo                                                                    | nmap -v <IP víctima>              |
| ``--stats-every=<número de segundos>`` | muestra stats del escaneo cada X segundos                                                                 | nmap --stats-every=5 <IP víctima> |
| ``--script=< script>``                 |                                                                                                           |                                   |
|                                        |                                                                                                           |                                   |
# NS engine
| `--script-help=<keyword>` | Filtra por keyword (CVE, versión X de servicio...)        |
| ------------------------- | --------------------------------------------------------- |
| `--script-updatedb`       | Actualizar db de scripts                                  |
| `--script vuln`           | Escáner general de vulnerabilidades                       |
| `--script "http-*"`       | Se pueden usar wildcards para escanear con varios scripts |
| `--script "discovery"`    | También se puede escanear con categorías enteras          |
https://nmap.org/book/nse-usage.html
[NSE categories](https://nmap.org/book/nse-usage.html#nse-categories)

## Recon

|**Categoría / Script**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|**HTTP Methods**|Identifica qué métodos HTTP están permitidos (GET, POST, PUT, DELETE, etc.).|`nmap --script http-methods <target>`|
|**DNS Brute**|Intenta descubrir subdominios mediante fuerza bruta usando un diccionario.|`nmap --script dns-brute <target>`|
|**Whois**|Realiza una consulta Whois para obtener datos del registro del dominio/IP.|`nmap --script whois-domain <target>`|
|**Banner**|Conecta a los puertos abiertos para capturar el banner de bienvenida del servicio.|`nmap --script banner <target>`|
|**SMB OS Discovery**|Determina el sistema operativo, nombre de equipo y grupo de trabajo vía SMB.|`nmap --script smb-os-discovery <target>`|
|**SSL Cert**|Recupera y analiza el certificado SSL/TLS de un servidor (emisor, fechas, etc.).|`nmap --script ssl-cert <target>`|
|**VULNERS**|Cruza las versiones de servicios detectadas con bases de datos de vulnerabilidades (CVE).|`nmap -sV --script vulners <target>`|
|**HTTP Enum**|Enumera directorios y archivos comunes en servidores web (similar a un fuzzing ligero).|`nmap --script http-enum <target>`|
|**FTP Anon**|Comprueba si el servidor FTP permite el acceso con el usuario "anonymous".|`nmap --script ftp-anon <target>`|
|**SSH Hostkey**|Recupera las claves públicas del host SSH para identificación y huella digital.|`nmap --script ssh-hostkey <target>`|



# Tips
### Añadir al final para generar un reporte HTML
``` bash
-oX nmap_1.xml && xsltproc nmap_1.xml -o nmap_1.html
```

### [Bypass tcpwrapped with nmap](https://security.stackexchange.com/questions/23407/how-to-bypass-tcpwrapped-with-nmap-scan)