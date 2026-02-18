
```bash
dirb <URL_base> [<archivo_lista_palabras>] [opciones]
```

|      |                                                                                      |                                                                                    |
| ---- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| `-a` | Especifica User-Agent personalizado.                                                 | dirb http://target.com -a "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Firefox/90.0" |
| `-c` | Establece una cookie para la solicitud                                               | dirb http://target.com -a "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Firefox/90.0" |
| `-H` | Modifica los headers                                                                 | dirb http://target.com -H "Authorization: Bearer token123"                         |
| `-i` | Modo case insensitive                                                                | dirb http://target.com -i                                                          |
| `-N` | Ignora ciertas respuestas                                                            | dirb http://target.com -N 404                                                      |
| `-o` | Guarda los resultados en un archivo                                                  | dirb http://target.com -o scan_results.txt                                         |
| `-p` | Utiliza un proxy                                                                     | dirb http://target.com -p 127.0.0.1:8080                                           |
| `-r` | Ignora los subdirectorios                                                            | dirb http://target.com -r                                                          |
| `-R` | Activa la recursión interactiva (pide confirmación antes de escanear subdirectorios) | dirb http://target.com -R                                                          |
| `-S` | Activa el modo silencioso                                                            | dirb http://target.com -S                                                          |
| `-X` | Especifica las extensiones a probar                                                  | dirb http://target.com -X .php,.html,.txt                                          |
| `-w` | Elimina las extensiones default al iterar por el diccionario                         | dirb http://target.com -w                                                          |
|      |                                                                                      |                                                                                    |
