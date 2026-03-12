# Google
| **Categoría**                   | **Operador / Sintaxis** | **Descripción Técnica Profunda**                              | **Ejemplo de Uso en Ciberseguridad**        |
| ------------------------------- | ----------------------- | ------------------------------------------------------------- | ------------------------------------------- |
| **Filtros de Contenido (Core)** | `site:`                 | Restringe resultados a un dominio, subdominio o TLD.          | `site:*.mil.us`                             |
|                                 | `filetype:` / `ext:`    | Filtra por la extensión del archivo según el encabezado MIME. | `ext:log OR ext:env OR ext:yaml`            |
|                                 | `intitle:`              | Busca el término en la etiqueta `<title>`.                    | `intitle:"index of" "backup"`               |
|                                 | `allintitle:`           | Fuerza a que **todas** las palabras estén en el título.       | `allintitle:admin login dashboard`          |
|                                 | `inurl:`                | Busca la cadena en cualquier parte de la URL.                 | `inurl:/phpinfo.php`                        |
|                                 | `allinurl:`             | Fuerza a que **todas** las palabras estén en la URL.          | `allinurl:auth setup config`                |
|                                 | `intext:`               | Busca el término en el cuerpo (body) del documento.           | `intext:"standard connection string"`       |
|                                 | `allintext:`            | Fuerza a que **todas** las palabras estén en el texto.        | `allintext:password "token"`                |
| **Relacionales y Enlaces**      | `link:`                 | Encuentra páginas que enlazan a una URL específica.           | `link:target.com`                           |
|                                 | `related:`              | Encuentra sitios que Google considera similares.              | `related:target.com`                        |
|                                 | `inanchor:`             | Busca términos en el texto de anclaje de los links.           | `inanchor:"vpn login"`                      |
|                                 | `allinanchor:`          | Todos los términos deben estar en el texto del link.          | `allinanchor:reset password`                |
| **Sistema y Metadatos**         | `cache:`                | Muestra la copia del sitio en el índice de Google.            | `cache:target.com/config.old`               |
|                                 | `info:`                 | Resumen de información que Google tiene del sitio.            | `info:target.com`                           |
|                                 | `source:`               | Filtra resultados de una fuente de noticias específica.       | `source:reuters "data breach"`              |
|                                 | `daterange:`            | Filtra por fecha en formato **Juliano** (específico).         | `site:target.com daterange:2460310-2460675` |
| **Búsqueda Social / App**       | `phonebook:`            | (Legacy/Specific) Busca números de teléfono.                  | `phonebook:TargetName`                      |
|                                 | `maps:`                 | Fuerza resultados de ubicaciones geográficas.                 | `maps: "data center" New York`              |
|                                 | `book:` / `isbn:`       | Busca dentro de Google Books o por código ISBN.               | `book: "cybersecurity" "cisco"`             |
|                                 | `@`                     | Busca en etiquetas de redes sociales.                         | `@twitter "leak"`                           |
|                                 | `#`                     | Busca hashtags específicos.                                   | `#infosec "target"`                         |
| **Operadores Lógicos**          | `""`                    | Búsqueda exacta de caracteres/frases.                         | `"X-Powered-By: PHP/5.3.3"`                 |
|                                 | `OR` / `\|`             | Operador booleano de unión.                                   | `ext:sql OR ext:dbf`                        |
|                                 | `-`                     | Operador de exclusión (NOT).                                  | `-inurl:html -inurl:php`                    |
|                                 | `*`                     | Comodín para una palabra completa.                            | `"config * password = *"`                   |
|                                 | `..`                    | Rango numérico (útil para años o puertos).                    | `site:target.com 2022..2026`                |
|                                 | `AROUND(n)`             | Proximidad: palabras separadas por max (n) términos.          | `"admin" AROUND(2) "password"`              |
|                                 | `+`                     | Fuerza la inclusión de una palabra (obsoleto pero útil).      | `+internal +confidential`                   |
|                                 | `&`                     | Funciona como AND (por defecto en Google).                    | `target & secret`                           |

# Bing
| **Categoría**          | **Operador / Sintaxis** | **Descripción Técnica**         | **Template / Ejemplo de Uso**      |
| ---------------------- | ----------------------- | ------------------------------- | ---------------------------------- |
| **Filtros Exclusivos** | `ip:`                   | Descubre dominios en una IP.    | `ip:13.107.246.10`                 |
|                        | `contains:`             | Páginas que contienen el link.  | `contains:pdf "manual"`            |
|                        | `feed:`                 | Busca RSS/Atom.                 | `feed:target.com`                  |
|                        | `hasfeed:`              | Páginas con feeds.              | `hasfeed:google`                   |
| **Filtros Estándar**   | `site:`                 | Restricción de dominio.         | `site:mil.us`                      |
|                        | `filetype:`             | Extensión de archivo.           | `filetype:xlsx`                    |
|                        | `instreamset:`          | Busca en metadatos específicos. | `instreamset:(url title):admin`    |
|                        | `inbody:`               | Texto en el cuerpo.             | `inbody:"confidential"`            |
|                        | `intitle:`              | Texto en el título.             | `intitle:"Index of"`               |
|                        | `loc:` / `language:`    | Región y lenguaje.              | `loc:UK language:en`               |
| **Lógica**             | `AND` / `&`             | Intersección.                   | `admin AND password`               |
|                        | `OR` / `\|`             | Disyunción.                     | `ext:pdf OR ext:doc`               |
|                        | `NOT` / `-`             | Exclusión.                      | `-site:bing.com`                   |
|                        | `()`                    | Agrupación de lógica.           | `(site:a.com OR site:b.com) admin` |
# Shodan
| **Categoría**              | **Operador / Sintaxis** | **Descripción Técnica**             | **Template / Ejemplo de Uso** |
| -------------------------- | ----------------------- | ----------------------------------- | ----------------------------- |
| **Red e Identidad**        | `net:`                  | Rango de red en formato CIDR.       | `net:216.58.192.0/19`         |
|                            | `ip:`                   | Resultados para una IP específica.  | `ip:8.8.8.8`                  |
|                            | `asn:`                  | Número de Sistema Autónomo.         | `asn:AS15169`                 |
|                            | `hostname:`             | Nombre del host o dominio.          | `hostname:google.com`         |
|                            | `org:`                  | Organización propietaria de la IP.  | `org:"Microsoft"`             |
|                            | `isp:`                  | Proveedor de servicios de Internet. | `isp:"Verizon"`               |
| **Servicio y App**         | `port:`                 | Puerto específico (TCP/UDP).        | `port:445,139` (SMB)          |
|                            | `product:`              | Nombre del software/servidor.       | `product:"nginx"`             |
|                            | `version:`              | Versión específica del software.    | `version:"1.14.0"`            |
|                            | `http.title:`           | Texto en el título HTML.            | `http.title:"Dashboard"`      |
|                            | `http.status:`          | Código de respuesta HTTP.           | `http.status:401`             |
|                            | `http.html:`            | Texto dentro del código fuente.     | `http.html:"password"`        |
|                            | `http.component:`       | Tecnologías web detectadas.         | `http.component:"react"`      |
| **Certificados/Seguridad** | `ssl:`                  | Busca en el certificado SSL.        | `ssl:"target.com"`            |
|                            | `ssl.cert.serial:`      | Serial del certificado.             | `ssl.cert.serial:12345`       |
|                            | `ssl.cert.subject.cn:`  | Common Name del certificado.        | `ssl.cert.subject.cn:"*.gov"` |
|                            | `vuln:`                 | Filtro por ID de CVE (Pago).        | `vuln:CVE-2017-0144`          |
| **Geolocalización**        | `country:` / `city:`    | Código ISO país / Ciudad.           | `country:ES city:"Barcelona"` |
|                            | `geo:`                  | Coordenadas (lat, lon).             | `geo:41.38,2.17`              |
| **Lógica**                 | `(Espacio)`             | Intersección (AND).                 | `port:21 product:proftpd`     |
|                            | `OR`                    | Unión (OR).                         | `port:80 OR port:443`         |
|                            | `-`                     | Exclusión (NOT).                    | `-port:80`                    |
# GitHub
| **Categoría**           | **Operador / Sintaxis** | **Descripción Técnica**    | **Template / Ejemplo de Uso**  |
| ----------------------- | ----------------------- | -------------------------- | ------------------------------ |
| **Filtros de Archivo**  | `filename:`             | Nombre exacto del archivo. | `filename:id_rsa`              |
|                         | `extension:`            | Extensión del archivo.     | `extension:env`                |
|                         | `path:`                 | Ruta de directorios.       | `path:src/config/`             |
|                         | `size:`                 | Tamaño en bytes.           | `size:>50000`                  |
|                         | `language:`             | Lenguaje de programación.  | `language:python`              |
| **Filtros de Contexto** | `user:`                 | Usuario específico.        | `user:torvalds`                |
|                         | `org:`                  | Organización específica.   | `org:google`                   |
|                         | `repo:`                 | Repositorio específico.    | `repo:owner/name`              |
|                         | `fork:`                 | Incluir o filtrar forks.   | `fork:true` / `fork:only`      |
|                         | `archived:`             | Repositorios archivados.   | `archived:false`               |
| **Filtros de Tiempo**   | `created:`              | Fecha de creación.         | `created:>2026-01-01`          |
|                         | `pushed:`               | Último commit.             | `pushed:<2025-12-31`           |
| **Lógica**              | `(Espacio)`             | AND lógico.                | `extension:sql password`       |
|                         | `OR`                    | OR lógico.                 | `"api_key" OR "client_secret"` |
|                         | `NOT` / `-`             | Exclusión.                 | `NOT "test"`                   |
|                         | `""`                    | Cadena exacta.             | `"HEROKU_API_KEY"`             |
