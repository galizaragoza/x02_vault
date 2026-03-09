Curl envía  y recibe información mediante distintos protocolos (HTTP, HTTPS, FTP...) y métodos (GET, POST, PUT, DELETE...), además, nos permite manipular solicitudes modificando cabeceras, cookies...

``` bash
curl [opciones] <URL>
```

|                     |                                         |                                                         |
| ------------------- | --------------------------------------- | ------------------------------------------------------- |
| `-X` o `--request`  | Especifica el método HTTP a utilizar    | curl -X POST https://target.com/api                     |
| `-H` o `--header`   | Personaliza las cabeceras               | curl -H "User-Agent: Mozilla/5.0" https://target.com    |
| `-d` o `--data`     | Envía datos en POST/PUT                 | curl -d "user=admin&pass=test" https://target.com/login |
| `-u` o `--user`     | Autenticación                           | curl -u admin:password https://target.com/restricted    |
| `-k` o `--insecure` | Ignora la verificación SSL              | curl -k https://insecure.target.com                     |
| `-L`                | Sigue rediracciones HTTP                | curl -L http://target.com/redirect                      |
| `-i`                | Muestra los headers de la respuesta     | curl -i https://target.com                              |
| `-v`                | Modo verbose                            | curl -v https://target.com                              |
| `-s`                | Modo silencioso                         |                                                         |
| `--cookie`          | Manejo de cookies                       | curl --cookie "session=abc123" https://target.com       |
| `-o`                | Guarda la respuesta en un archivo       | curl -o output.html https://target.com                  |
| `-O`                | Descarga un archivo con nombre original | curl -O http://target.com/file.zip                      |
|                     |                                         |                                                         |
# Protips

|                    |                |
| ------------------ | -------------- |
| `curl ifconfig.me` | Ver IP pública |
