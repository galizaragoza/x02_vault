> WPScan es una herramienta que nos permite extraer todo tipo de información de sitios web hechos con WordPress, puede detectar vulnerabilidades, temas, plugins, usuarios, passwords...

**Funciones principales**
- **Escaneo de vulnerabilidades:** Detecta fallos en WordPress, plugins y temas.
- **Enumeración:** Identifica usuarios, plugins, temas y configuraciones expuestas.
- **Ataques de fuerza bruta:** Prueba credenciales con listas de usuarios/contraseñas.
- **Detección de configuraciones débiles:** Encuentra problemas como directorios expuestos o copias de wp-config.php.
- **Integración con API:** Usa un token para acceder a datos actualizados de vulnerabilidades.
```
wpscan --url <URL> [opciones]
```

|                       |                                                          |                                                                     |
| --------------------- | -------------------------------------------------------- | ------------------------------------------------------------------- |
| `--url`               | Especifica el sitio WP a escanear                        | wpscan --url http://example.com                                     |
| `-e`                  | Enumera ciertos elementos$^1$                            | wpscan --url http://example.com -e u                                |
| `--api-token`         | Usa un token para acceder a la DB de vulnerabilidades    | wpscan --url http://example.com --api-token YOUR_TOKEN -e vp        |
| `--plugins-detection` | Define el modo de detección (passive, aggressive, mixed) | wpscan --url http://example.com -e p --plugins-detection aggressive |
| `-U` `-P`             | Realiza un ataque diccionario                            | wpscan --url http://example.com -U admin -P rockyou.txt             |
| `--random-user-agent` | Usa un User Agent aleatorio para evitar detección        | wpscan --url http://example.com --random-user-agent                 |
| `--output`            | Guarda los resultados en un archivo                      | wpscan --url http://example.com -o scan_results.txt                 |
| `--throttle`          | Añade delay entre solicitudes (en ms)                    | wpscan --url http://example.com --throttle 1000                     |
| `--update`            | Actualiza la base de datos                               | wpscan --update                                                     |
|                       |                                                          |                                                                     |
	1 --> u: usuarios, p: plugins, vp: plugins vulnerables, vt: temas vulnerables