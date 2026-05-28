Feroxbuster es un fuzzer ultrarápido escrito en Rust, ideal para la mayoría de tareas de fuzzing, como encontrar archivos, recursión, reconocimiento, según Grok, es idea y estándar para el 90% de tareas de fuzzing, siendo superado por [[FFUF]] para casos específicos como:
- Fuzzing de parámetros
- Evadir WAF
- Wordlists muy grandes
- Fuzzing complejo/muy customizado
En definitiva, es excelente en las tareas comunes de reconocimiento y pentesting.

| Opción                    | Función principal                                         | Ejemplo práctico                  |
| ------------------------- | --------------------------------------------------------- | --------------------------------- |
| `-u, --url URL`           | URL objetivo (obligatorio)                                | `-u https://ejemplo.com`          |
| `-w, --wordlist file`     | Wordlist personalizada (default: raft-medium-directories) | `-w /ruta/big.txt`                |
| `-x, --extension ext`     | Extensiones a probar (añade .php,.bak,.txt, etc.)         | `-x php,js,bak,txt,zip`           |
| `-t, --threads int`       | Número de threads (default: 50)                           | `-t 200`                          |
| `-k, --no-tls-validation` | Ignora certificados SSL inválidos                         | `-k`                              |
| `-E`                      | Sigue redirecciones automáticamente                       | `-E`                              |
| `-s, --status-codes`      | Códigos HTTP a mostrar (default: 200,301,404, etc.)       | `-s 200,301,302`                  |
| `-C, --filter-status`     | Oculta códigos especificados                              | `-C 404,403`                      |
| `-S, --filter-size`       | Oculta respuestas por tamaño en bytes                     | `-S 0,1234`                       |
| `-q, --quiet`             | Solo muestra URLs encontradas (ideal para pipes)          | `-q`                              |
| `-o, --output file`       | Guarda resultados en archivo                              | `-o resultados.txt`               |
| `-a, --user-agent`        | User-Agent personalizado                                  | `-a "Mozilla/5.0 (XSS)"`          |
| `--auto-tune`             | Ajusta automáticamente threads según latencia             | `--auto-tune`                     |
| `--depth int`             | Profundidad máxima de recursión (default: 4)              | `--depth 6`                       |
| `-r, --redirector URL`    | Usa redirector externo para evadir bloqueos               | `-r http://127.0.0.1:8080`        |
| `--proxy URL`             | Proxy (socks5/http)                                       | `--proxy socks5://127.0.0.1:1080` |

## Cheats
```
-x php,html,conf,config,txt,js,py,bak,env,jar,csv,xml,md,pdf,zip,rar,jpg,jpeg,png,db,sql,log,json
```
Lista de extensiones grande

```
-x php,html,txt,js,py,md,zip,rar,jpg,png,log,json
```
Lista chill para labs

```
feroxbuster -u <<URL>> -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,html,txt,js,json,log,zip -k -E -q --auto-tune
```
Comando para escaneo general, añadir `-n` para quitar recursión.