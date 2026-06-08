``` bash
nmap -sV -p 1-65535 192.168.1.1
```
# Parámetros

## Objetivos
|**Parámetro**|**Descripción**|**Ejemplo de uso**|
|---|---|---|
|**`-iL`**|Lee los objetivos desde un archivo de texto.|`nmap -iL hosts.txt`|
|**`-iR`**|Elige objetivos al azar (útil para estudios estadísticos).|`nmap -iR 100`|
|**`--exclude`**|Excluye hosts o redes específicas del escaneo.|`nmap 192.168.1.0/24 --exclude 192.168.1.1`|
|**`-sn`**|**Ping Scan**: Desactiva el escaneo de puertos (solo detecta si el host vive).|`nmap -sn <IP>`|
|**`-Pn`**|Asume que el host está activo (omite el ping). Útil contra firewalls.|`nmap -Pn <IP>`|
|**`-n`**|**No DNS**: Deshabilita la resolución inversa de nombres (más rápido).|`nmap -n <IP>`|
|**`-R`**|Fuerza la resolución DNS de todos los objetivos.|`nmap -R <IP>`|
## Scan techniques
|**Parámetro**|**Descripción**|**Ejemplo de uso**|
|---|---|---|
|**`-sS`**|**TCP SYN (Stealth)**: Rápido y discreto, no completa el 3-way handshake.|`nmap -sS <IP>`|
|**`-sT`**|**TCP Connect**: Realiza el handshake completo (más ruidoso).|`nmap -sT <IP>`|
|**`-sU`**|**UDP Scan**: Escanea puertos UDP (DNS, DHCP, SNMP, etc.).|`nmap -sU <IP>`|
|**`-sA`**|**ACK Scan**: Se usa para mapear reglas de firewall.|`nmap -sA <IP>`|
|**`-sN / -sF / -sX`**|Scans Null, FIN y Xmas (usados para burlar ciertos firewalls).|`nmap -sX <IP>`|
|**`-sY`**|Escaneo de protocolos SCTP INIT.|`nmap -sY <IP>`|
|**`-sO`**|Escaneo de protocolos IP (determina qué protocolos (TCP, ICMP, etc) soporta).|`nmap -sO <IP>`|
## Detección

| **Parámetro**       | **Descripción**                                                | **Ejemplo de uso**   |
| ------------------- | -------------------------------------------------------------- | -------------------- |
| **`-p-`**           | Escanea **todos** los 65535 puertos.                           | `nmap -p- <IP>`      |
| **`-p <rango>`**    | Escanea puertos específicos (ej: 80,443 o 1-100).              | `nmap -p 22,80 <IP>` |
| **`--open`**        | Muestra **únicamente** los puertos que están abiertos.         | `nmap --open <IP>`   |
| **`-F`**            | **Fast Mode**: Escanea menos puertos que el modo por defecto.  | `nmap -F <IP>`       |
| **`-sV`**           | **Versión**: Detecta versiones de servicios y software.        | `nmap -sV <IP>`      |
| **`-O`**            | **OS Detection**: Intenta determinar el sistema operativo.     | `nmap -O <IP>`       |
| **`-A`**            | **Modo Agresivo**: Activa `-sV`, `-sC`, `-O` y `--traceroute`. | `nmap -A <IP>`       |
| `--top-ports <num>` | Escanea los X puertos más comunes                              |                      |
## Scripts y velocidad
| **Parámetro**         | **Descripción**                                                 | **Ejemplo de uso**             |
| --------------------- | --------------------------------------------------------------- | ------------------------------ |
| **`-sC`**             | Ejecuta los scripts por defecto de Nmap (NSE).                  | `nmap -sC <IP>`                |
| **`--script=<name>`** | Ejecuta un script o categoría específica (auth, vuln, exploit). | `nmap --script=vuln <IP>`      |
| **`-T<0-5>`**         | Ajusta la agresividad temporal (0: Paranoid, 5: Insane).        | `nmap -T4 <IP>`                |
| **`--min-rate`**      | Establece el mínimo de paquetes enviados por segundo.           | `nmap --min-rate 5000 <IP>`    |
| **`--host-timeout`**  | Abandona un host si no responde en el tiempo indicado.          | `nmap --host-timeout 30m <IP>` |

## Firewall / IDS
|**Parámetro**|**Descripción**|**Ejemplo de uso**|
|---|---|---|
|**`-f`**|Fragmenta los paquetes para dificultar la detección del IDS/Firewall.|`nmap -f <IP>`|
|**`-D <D1,D2>`**|**Decoys**: Enmascara tu IP usando señuelos.|`nmap -D RND:10 <IP>`|
|**`-S <IP>`**|Spoofing: Falsea tu dirección IP de origen.|`nmap -S 1.1.1.1 <IP>`|
|**`-v / -vv`**|Aumenta el nivel de detalle (verbose) en la pantalla.|`nmap -vv <IP>`|
|**`-oA <name>`**|Guarda el resultado en los 3 formatos principales (Normal, XML, Grep).|`nmap -oA scan_result <IP>`|
|**`--stats-every`**|Muestra el progreso del escaneo cada X tiempo.|`nmap --stats-every 10s <IP>`|
|**`--reason`**|Explica por qué Nmap determinó que un puerto está abierto/cerrado.|`nmap --reason <IP>`|


# NS engine

| `ls /usr/share/nmap/scripts` | Lista todos los scripts instalados                        |
| ---------------------------- | --------------------------------------------------------- |
| `--script-help=<keyword>`    | Filtra por keyword (CVE, versión X de servicio...)        |
| `--script-updatedb`          | Actualizar db de scripts                                  |
| `--script vuln`              | Escáner general de vulnerabilidades                       |
| `--script "http-*"`          | Se pueden usar wildcards para escanear con varios scripts |
| `--script "discovery"`       | También se puede escanear con categorías enteras          |
https://nmap.org/book/nse-usage.html
[NSE categories](https://nmap.org/book/nse-usage.html#nse-categories)

## Recon

| **Categoría / Script** | **Función**                                                                               | **Ejemplo de Sintaxis**                   |
| ---------------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------- |
| **HTTP Methods**       | Identifica qué métodos HTTP están permitidos (GET, POST, PUT, DELETE, etc.).              | `nmap --script http-methods <target>`     |
| **DNS Brute**          | Intenta descubrir subdominios mediante fuerza bruta usando un diccionario.                | `nmap --script dns-brute <target>`        |
| **Whois**              | Realiza una consulta Whois para obtener datos del registro del dominio/IP.                | `nmap --script whois-domain <target>`     |
| **Banner**             | Conecta a los puertos abiertos para capturar el banner de bienvenida del servicio.        | `nmap --script banner <target>`           |
| **SMB OS Discovery**   | Determina el sistema operativo, nombre de equipo y grupo de trabajo vía SMB.              | `nmap --script smb-os-discovery <target>` |
| **SSL Cert**           | Recupera y analiza el certificado SSL/TLS de un servidor (emisor, fechas, etc.).          | `nmap --script ssl-cert <target>`         |
| **VULNERS**            | Cruza las versiones de servicios detectadas con bases de datos de vulnerabilidades (CVE). | `nmap -sV --script vulners <target>`      |
| **HTTP Enum**          | Enumera directorios y archivos comunes en servidores web (similar a un fuzzing ligero).   | `nmap --script http-enum <target>`        |
| **FTP Anon**           | Comprueba si el servidor FTP permite el acceso con el usuario "anonymous".                | `nmap --script ftp-anon <target>`         |
| **SSH Hostkey**        | Recupera las claves públicas del host SSH para identificación y huella digital.           | `nmap --script ssh-hostkey <target>`      |

# Scripts
### SSL certificate
```
nmap --script ssl-cert -p 443 [IP]
```

# Tips
### Añadir al final para generar un reporte HTML
``` bash
-oX nmap_1.xml && xsltproc nmap_1.xml -o nmap_1.html
```

### [Bypass tcpwrapped with nmap](https://security.stackexchange.com/questions/23407/how-to-bypass-tcpwrapped-with-nmap-scan)