**Arjun** es una herramienta de búsqueda de parámetros HTTP (HTTP parameter discovery). Su función principal es encontrar parámetros ocultos en puntos de enlace (endpoints) que podrían ser vulnerables a ataques como XSS, SQLi o SSRF.

| **Parámetro**       | **Función**                                         | **Ejemplo de Sintaxis**                 |
| ------------------- | --------------------------------------------------- | --------------------------------------- |
| `-u` / `--url`      | Especifica la URL del objetivo.                     | `arjun -u https://api.site.com/v1/user` |
| `-m` / `--method`   | Método HTTP a utilizar (GET, POST, XML, JSON).      | `arjun -u URL -m POST`                  |
| `-w` / `--wordlist` | Ruta a un diccionario de parámetros personalizado.  | `arjun -u URL -w /path/dict.txt`        |
| `-t` / `--threads`  | Número de hilos concurrentes para el escaneo.       | `arjun -u URL -t 20`                    |
| `-o` / `--output`   | Guarda los resultados en un archivo.                | `arjun -u URL -o result.json`           |
| `--headers`         | Añade cabeceras personalizadas (ej. cookies).       | `arjun -u URL --headers "Cookie: id=1"` |
| `--include`         | Incluye datos persistentes en todas las peticiones. | `arjun -u URL --include '{"id":"123"}'` |
| `--delay`           | Tiempo de espera entre peticiones (evita WAF).      | `arjun -u URL --delay 2`                |
| `--rate-limit`      | Límite máximo de peticiones por segundo.            | `arjun -u URL --rate-limit 10`          |