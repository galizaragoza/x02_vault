**Gobuster** es una herramienta escrita en Go utilizada para realizar ataques de fuerza bruta sobre URIs (directorios y archivos), subdominios DNS, hosts virtuales y buckets de almacenamiento. Es extremadamente rápida gracias a su concurrencia nativa.
```
gobuster [modo] [opciones]
```

| **Parámetro**      | **Función**                                                 | **Ejemplo de Sintaxis**                                     |
| ------------------ | ----------------------------------------------------------- | ----------------------------------------------------------- |
| `-u`, `--url`      | URL con la palabra clave `FUZZ`.                            | `gobuster fuzz -u http://example.com/api?id=FUZZ`           |
| `-w`, `--wordlist` | Diccionario con los valores para el fuzzer.                 | `gobuster fuzz -u [URL] -w payloads.txt`                    |
| `-m`, `--method`   | Método HTTP a utilizar (GET, POST, etc.).                   | `gobuster fuzz -u [URL] -w [LIST] -m POST`                  |
| `-p`, `--proxy`    | Envía las peticiones a través de un proxy (ej. Burp Suite). | `gobuster fuzz -u [URL] -w [LIST] -p http://127.0.0.1:8080` |
| `-v`               | Modo verbose                                                |                                                             |
## dir
| **Parámetro**         | **Función**                                           | **Ejemplo de Sintaxis**                                                                 |
| --------------------- | ----------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `-u`, `--url`         | Especifica la URL completa del objetivo.              | `gobuster dir -u https://example.com/`                                                  |
| `-w`, `--wordlist`    | Ruta al diccionario de directorios/archivos.          | `gobuster dir -w /usr/share/wordlists/dirb/common.txt`                                  |
| `-x`, `--extensions`  | Extensiones de archivo a buscar.                      | `gobuster dir -u [URL] -w [LIST] -x php,txt,html`                                       |
| `-f`, `--add-slash`   | Añade un `/` al final de cada petición a directorios. | `gobuster dir -u [URL] -w [LIST] -f`                                                    |
| `-s`, `--statuscodes` | Códigos de estado HTTP positivos.                     | `gobuster dir -u [URL] -w [LIST] -s "200,204,301"`                                      |
| `-b`, `--blacklisted` | Códigos de estado HTTP a ignorar (negativos).         | `gobuster dir -u [URL] -w [LIST] -b "404,403"`                                          |
| `-t`                  | Establece el núm. de hilos a utilizar                 | gobuster dir -u https://example.com -w wordlist.txt -t 50                               |
| `-H`                  | Añade Headers personalizados                          | gobuster dir -u https://example.com -w wordlist.txt -H "Authorization: Bearer token123" |
| `-c`                  | Permite enviar una cookie personalizada               | gobuster dir -u https://example.com -w wordlist.txt -c "session=abc123"                 |
| `-k`                  | Omite la verificación de SSL                          | gobuster dir -u https://example.com -w wordlist.txt -k                                  |
| `-o`                  | Guarda los resultados en un archivo                   | gobuster dir -u https://example.com -w wordlist.txt -o results.txt                      |
| `-l`                  | Muestra el tamaño de las respuestas                   | gobuster dir -u https://example.com -w wordlist.txt -l                                  |
| `--xl`                | Excluye respuestas de un tamaño específico            | gobuster dir -u https://example.com -w wordlist.txt --exclude-length 9265               |
| `--useragent`         | Definir el user agent                                 |                                                                                         |
| `--random-agent`      | Utiliza un valor UA random                            |                                                                                         |

## dns
| **Parámetro**        | **Función**                                             | **Ejemplo de Sintaxis**                      |
| -------------------- | ------------------------------------------------------- | -------------------------------------------- |
| `-d`, `--domain`     | Dominio principal a investigar.                         | `gobuster dns -d example.com`                |
| `-w`, `--wordlist`   | Ruta al diccionario de nombres de subdominios.          | `gobuster dns -d [DOM] -w subdomains.txt`    |
| `-r`, `--resolver`   | Servidor DNS personalizado para las consultas.          | `gobuster dns -d [DOM] -w [LIST] -r 1.1.1.1` |
| `-i`, `--show-ips`   | Muestra las direcciones IP de los subdominios hallados. | `gobuster dns -d [DOM] -w [LIST] -i`         |
| `-c`, `--show-cname` | Muestra los registros CNAME (alias).                    | `gobuster dns -d [DOM] -w [LIST] -c`         |
| `--wildcard`         | Detecta e ignora IPs de subdominios "comodín".          | `gobuster dns -d [DOM] -w [LIST] --wildcard` |

## vhost
| **Parámetro**      | **Función**                                                | **Ejemplo de Sintaxis**                                  |
| ------------------ | ---------------------------------------------------------- | -------------------------------------------------------- |
| `-u`, `--url`      | URL del servidor donde se alojan los vhosts.               | `gobuster vhost -u http://10.10.10.5`                    |
| `-w`, `--wordlist` | Diccionario de posibles nombres de hosts.                  | `gobuster vhost -u [URL] -w vhosts.txt`                  |
| `--append-domain`  | Añade automáticamente el dominio principal a cada palabra. | `gobuster vhost -u [URL] -w [LIST] --append-domain`      |
| `--domain`         | Dominio base para usar con `--append-domain`.              | `gobuster vhost -u [URL] -w [LIST] --domain example.com` |
| `--exclude-length` | Ignora respuestas con una longitud de cuerpo específica.   | `gobuster vhost -u [URL] -w [LIST] --exclude-length 302` |

## s3
|**Parámetro**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`-w`, `--wordlist`|Diccionario de nombres de buckets.|`gobuster s3 -w buckets_list.txt`|
|`-m`, `--max-files`|Número máximo de archivos a listar si el bucket es público.|`gobuster s3 -w [LIST] -m 100`|
|`--region`|Región de AWS específica para realizar la búsqueda.|`gobuster s3 -w [LIST] --region us-east-1`|
|`-v`, `--verbose`|Muestra errores detallados (útil para depuración).|`gobuster s3 -w [LIST] -v`|

## fuzz

|**Parámetro**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`-u`, `--url`|URL con la palabra clave `FUZZ`.|`gobuster fuzz -u http://example.com/api?id=FUZZ`|
|`-w`, `--wordlist`|Diccionario con los valores para el fuzzer.|`gobuster fuzz -u [URL] -w payloads.txt`|
|`-m`, `--method`|Método HTTP a utilizar (GET, POST, etc.).|`gobuster fuzz -u [URL] -w [LIST] -m POST`|
|`-p`, `--proxy`|Envía las peticiones a través de un proxy (ej. Burp Suite).|`gobuster fuzz -u [URL] -w [LIST] -p http://127.0.0.1:8080`|

## Cheats
```
php,html,db,conf,config,txt,js,py,bak,conf,env,jar,csv,xml,md,pdf,zip,rar,jpg,jpeg,png,db,sql,log,json
```