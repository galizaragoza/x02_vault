**theHarvester** es una herramienta de OSINT (Open Source Intelligence) diseñada para recolectar correos electrónicos, nombres de subdominios, IPs y nombres de empleados de diversas fuentes públicas.

| **Parámetro**          | **Función**                                                      | **Ejemplo de Sintaxis**                             |
| ---------------------- | ---------------------------------------------------------------- | --------------------------------------------------- |
| `-d`, `--domain`       | Dominio o empresa a investigar.                                  | `theHarvester -d microsoft.com`                     |
| `-b`, `--source`       | Fuente de datos (google, bing, linkedin, all).                   | `theHarvester -d apple.com -b google`               |
| `-l`, `--limit`        | Limita el número de resultados (por defecto 500).                | `theHarvester -d target.com -b all -l 200`          |
| `-v`, `--virtual-host` | Verifica nombres de host mediante resolución DNS y busca vhosts. | `theHarvester -d target.com -b bing -v`             |
| `-f`, `--filename`     | Guarda los resultados en un archivo HTML y XML.                  | `theHarvester -d target.com -b all -f audit_report` |
| `-n`, `--dns-lookup`   | Realiza una consulta DNS sobre todos los nombres encontrados.    | `theHarvester -d target.com -b google -n`           |