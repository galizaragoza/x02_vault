**DNSRecon** es una de las herramientas más potentes para el reconocimiento de DNS. Permite realizar desde transferencias de zona hasta enumeración de subdominios y registros SRV.

| **Parámetro**         | **Función**                                                                                                              | **Ejemplo de Sintaxis**                      |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------- |
| `-d`, `--domain`      | Especifica el dominio objetivo.                                                                                          | `dnsrecon -d example.com`                    |
| `-t`, `--type`        | Especifica el tipo de enumeración (std, brt, axfr, rvl).                                                                 | `dnsrecon -d example.com -t brt`             |
| `-D`, `--dictionary`  | Archivo de diccionario para ataques de fuerza bruta.                                                                     | `dnsrecon -d example.com -t brt -D subs.txt` |
| `-n`, `--name_server` | Especifica un servidor DNS concreto para realizar las consultas.                                                         | `dnsrecon -d example.com -n 8.8.8.8`         |
| `-j`, `--json`        | Guarda la salida en un archivo con formato JSON.                                                                         | `dnsrecon -d example.com --json output.json` |
| `-r`, `--range`       | Realiza una búsqueda inversa de DNS en un rango de IPs (IP/CIDR).                                                        | `dnsrecon -r 192.168.1.0/24`                 |
| `-f`                  | Filter out of brute force domain lookup, records that resolve to the wildcard defined IP address when saving records.    |                                              |
| `-a`                  | Perform AXFR with standard enumeration.                                                                                  |                                              |
| `-s`                  | Perform a reverse lookup of IPv4 ranges in the SPF record with standard enumeration.                                     |                                              |
| `-b`                  | Perform Bing enumeration with standard enumeration.                                                                      |                                              |
| `-y`                  | Perform Yandex enumeration with standard enumeration.                                                                    |                                              |
| `-k`                  | Perform crt.sh enumeration with standard enumeration                                                                     |                                              |
| `-w`                  | Perform deep whois record analysis and reverse lookup of IP ranges found through Whois when doing a standard enumeration |                                              |
| `-z`                  | Performs a DNSSEC zone walk with standard enumeration.                                                                   |                                              |





