Sherlock es una herramienta de código abierto escrita en Python diseñada para buscar nombres de usuario en redes sociales.

| **Parámetro/Flag**           | **Función**                                                   | **Sintaxis de Ejemplo**                                |
| ------------------------ | --------------------------------------------------------- | -------------------------------------------------- |
| `username` (obligatorio) | Nombre de usuario a buscar (sin parámetro, solo el valor) | `sherlock johndoe`                                 |
| `--site SITENAME`        | Buscar solo en un sitio específico                        | `sherlock johndoe --site twitter`                  |
| `--timeout TIMEOUT`      | Tiempo máximo de espera por sitio (segundos)              | `sherlock johndoe --timeout 10`                    |
| `--print-found`          | Mostrar solo sitios donde se encontró el usuario          | `sherlock johndoe --print-found`                   |
| `--no-color`             | Desactivar colores en la salida                           | `sherlock johndoe --no-color`                      |
| `--csv`                  | Exportar resultados a CSV                                 | `sherlock johndoe --csv`                           |
| `--json`                 | Exportar resultados a JSON                                | `sherlock johndoe --json`                          |
| `--html`                 | Exportar resultados a HTML                                | `sherlock johndoe --html`                          |
| `--proxy PROXY_URL`      | Usar proxy HTTP/S o SOCKS                                 | `sherlock johndoe --proxy socks5://127.0.0.1:9050` |
| `--tor`                  | Usar Tor como proxy (requiere Tor en puerto 9050)         | `sherlock johndoe --tor`                           |
| `--unique-tor`           | Usar nuevo circuito Tor para cada petición                | `sherlock johndoe --unique-tor`                    |
| `--browse`               | Abrir enlaces encontrados en navegador                    | `sherlock johndoe --browse`                        |
| `--local`                | Usar solo datos locales (sin internet)                    | `sherlock johndoe --local`                         |
| `--nsfw`                 | Incluir sitios NSFW (no seguros para trabajo)             | `sherlock johndoe --nsfw`                          |
| `-f FILE`/`--file FILE`  | Cargar múltiples usuarios desde archivo                   | `sherlock --file usuarios.txt`                     |
| `-o DIR`/`--output DIR`  | Directorio para guardar resultados                        | `sherlock johndoe -o resultados/`                  |
| `-fo`/`--folderoutput`   | Crear carpeta por usuario para resultados                 | `sherlock johndoe -fo`                             |
| `--verbose`              | Modo detallado con más información                        | `sherlock johndoe --verbose`                       |
| `--version`              | Mostrar versión de Sherlock                               | `sherlock --version`                               |
