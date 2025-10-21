
``` bash
nmap -sV -p 1-65535 192.168.1.1
```
## Parámetros

|                                    |                                                                                                           |                                   |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------- | --------------------------------- |
| -p-                                | escanea todos los 65535 puertos                                                                           | nmap -p- <IP víctima>             |
| --open                             | muestra solo los puertos abiertos                                                                         | nmap --open <IP víctima>          |
| -T0  a -T5                         | controla la agresividad del escaneo, siendo 0 discreto y lento y 5 agresivo e intrusivo                   | nmap -T3 <IP víctima>             |
| --min-rate=<número>                | establece los paquetes por segundo                                                                        | nmap --min-rate=5000 <IP víctima> |
| -sV                                | detecta las versiones de los servicios                                                                    | nmap -sV <IP víctima>             |
| -sC                                | lanza comprobaciones adicionales en los puertos abiertos para identificar los servicios siendo ejecutados | nmap -sC <IP víctima>             |
| -O                                 | detecta el sistema operativo                                                                              | nmap -O <IP víctima>              |
| --traceroute                       | realiza un seguimiento de la ruta que toma un paquete de datos desde el host                              | nmap --traceroute <IP víctima>    |
| -A                                 | junta en un solo parámetro -sV, -sC, -O y --traceroute                                                    | nmap -A <IP víctima>              |
| -sT                                | realiza un escaneo TCP completo con 3-way handshake                                                       | nmap -sT <IP víctima>             |
| -sS                                | realiza un escaneo TCP completo sin 3-way handshake                                                       | nmap -sS <IP víctima>             |
| -sU                                | realiza un escaneo UDP completo                                                                           | nmap -sU <IP víctima>             |
| -sY                                | realiza un escaneo SCTP                                                                                   | nmap -sY<IP víctima>              |
| -Pn                                | asume que el host está activo y omite el ping                                                             | nmap -Pn <IP víctima>             |
| -n                                 | deshabilita la traducción DNS                                                                             | nmap -n <IP víctima>              |
| -v                                 | (verbose) muestra detalles del escaneo                                                                    | nmap -v <IP víctima>              |
| --stats-every=<número de segundos> | muestra stats del escaneo cada X segundos                                                                 | nmap --stats-every=5 <IP víctima> |
### Añadir al final para generar un reporte HTML
``` bash
-oX nmapScan.xml && xsltproc nmapScan.xml -o nmapScan.html && open nmapScan.html &>/dev/null & disown
```