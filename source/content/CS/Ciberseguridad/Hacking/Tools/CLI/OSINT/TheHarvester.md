
| **Parámetro**      | **Función**                                           | **Sintaxis de Ejemplo**                         |
| -------------- | ------------------------------------------------- | ------------------------------------------- |
| `-d`           | Especifica el dominio objetivo                    | `theHarvester -d ejemplo.com`               |
| `-l`           | Limita el número de resultados devueltos          | `theHarvester -d ejemplo.com -l 100`        |
| `-b`           | Especifica las fuentes de datos a utilizar        | `theHarvester -d ejemplo.com -b google`     |
| `-s`           | Inicia un escaneo en profundidad                  | `theHarvester -d ejemplo.com -s`            |
| `-g`           | Utiliza Google Dorks para la búsqueda             | `theHarvester -d ejemplo.com -g`            |
| `-f`           | Guarda los resultados en un archivo HTML/XML      | `theHarvester -d ejemplo.com -f resultados` |
| `-n`           | Realiza una búsqueda DNS reversa                  | `theHarvester -d ejemplo.com -n`            |
| `-c`           | Realiza una búsqueda en Shodan                    | `theHarvester -d ejemplo.com -c`            |
| `-t`           | Realiza una búsqueda en ThreatCrowd               | `theHarvester -d ejemplo.com -t`            |
| `-v`           | Verifica el nombre del host mediante búsqueda DNS | `theHarvester -d ejemplo.com -v`            |
| `-h`           | Muestra el menú de ayuda                          | `theHarvester -h`                           |
| `--screenshot` | Toma capturas de pantalla de los dominios         | `theHarvester -d ejemplo.com --screenshot`  |