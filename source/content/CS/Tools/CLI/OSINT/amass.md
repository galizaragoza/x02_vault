
# Intel
| **Parámetro** | **Función**                                                      | **Ejemplo de sintaxis**             |
| ------------- | ---------------------------------------------------------------- | ----------------------------------- |
| `-active`     | Intenta obtener certificados TLS de los rangos de IP encontrados | `amass intel -active -asn 12345`    |
| `-addr`       | IPs para buscar nombres de dominio relacionados                  | `amass intel -addr 192.168.1.1`     |
| `-asn`        | Enumera basándose en el número de sistema autónomo               | `amass intel -asn 13335`            |
| `-cidr`       | Busca dominios en un bloque CIDR específico                      | `amass intel -cidr 104.16.0.0/12`   |
| `-d`          | Especifica el dominio objetivo                                   | `amass intel -d google.com`         |
| `-ip`         | Muestra la dirección IP de los dominios encontrados              | `amass intel -d example.com -ip`    |
| `-whois`      | Realiza consultas WHOIS para encontrar dominios relacionados     | `amass intel -whois -d example.com` |
|               |                                                                  |                                     |

# Enum
| **Parámetro** | **Función**                                                          | **Ejemplo de sintaxis**                       |
| ------------- | -------------------------------------------------------------------- | --------------------------------------------- |
| `-active`     | Habilita métodos de verificación activa (ej. transferencias de zona) | `amass enum -active -d target.com`            |
| `-brute`      | Realiza fuerza bruta de subdominios                                  | `amass enum -brute -d target.com`             |
| `-config`     | Especifica el archivo de configuración (API keys, etc.)              | `amass enum -config config.ini -d target.com` |
| `-d`          | El dominio principal a investigar                                    | `amass enum -d target.com`                    |
| `-df`         | Archivo con una lista de dominios objetivos                          | `amass enum -df domains.txt`                  |
| `-dir`        | Directorio de salida para la base de datos y logs                    | `amass enum -dir ./results -d target.com`     |
| `-ip`         | Muestra las direcciones IP de cada subdominio                        | `amass enum -ip -d target.com`                |
| `-o`          | Guarda los resultados en un archivo de texto                         | `amass enum -d target.com -o out.txt`         |
| `-passive`    | Realiza una búsqueda puramente pasiva (sin tocar al objetivo)        | `amass enum -passive -d target.com`           |
| `-src`        | Muestra de qué fuente (fuente de datos) proviene el subdominio       | `amass enum -src -d target.com`               |

# Viz
| **Parámetro** | **Función**                                       | **Ejemplo de sintaxis**       |
| ------------- | ------------------------------------------------- | ----------------------------- |
| `-d3`         | Genera un archivo HTML basado en D3.js            | `amass viz -d3 -d target.com` |
| `-dot`        | Genera un archivo en formato Graphviz DOT         | `amass viz -dot`              |
| `-gexf`       | Genera un archivo para Gephi (Graph Exchange XML) | `amass viz -gexf`             |