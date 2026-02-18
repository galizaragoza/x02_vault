 ### **Funciones principales**
1. **Enumeración de directorios/archivos**: Descubre directorios y archivos ocultos en servidores web.
2. **Enumeración de subdominios**: Identifica subdominios mediante resolución DNS.
3. **Enumeración de hosts virtuales**: Detecta hosts virtuales en un servidor web.
4. **Enumeración de buckets**: Busca buckets de almacenamiento (S3, Google Cloud) accesibles.
5. **Fuzzing personalizado**: Prueba parámetros, cabeceras o datos en solicitudes HTTP.
### **Usos comunes en pentesting y ciberseguridad**
- **Reconocimiento**: Encuentra endpoints ocultos, paneles de administración o archivos sensibles.
- **Pruebas de vulnerabilidades**: Identifica recursos para explotar SQLi, XSS o configuraciones débiles.
- **Bug bounties**: Descubre subdominios o buckets no listados.
- **Auditorías web**: Mapea superficies de ataque para evaluar seguridad.
- **Integración**: Combina con herramientas como Burp Suite o SecLists para análisis avanzados.
```
gobuster [modo] [opciones]
```

|                  |                                                       |                                                                                         |
| ---------------- | ----------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `-u`             | Especifica la URL o dominio a atacar                  | gobuster dir -u https://example.com -w wordlist.txt                                     |
| `-w`             | Define la ruta del diccionario                        | gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt             |
| `-x`             | Define la extensiones a probar                        | gobuster dir -u https://example.com -w wordlist.txt -x php,html,txt                     |
| `-t`             | Establece el núm. de hilos a utilizar                 | gobuster dir -u https://example.com -w wordlist.txt -t 50                               |
| `-s`             | Solo muestra ciertos códigos de estado HTTP           | gobuster dir -u https://example.com -w wordlist.txt -s 200,301,302                      |
| `-b`             | Excluye ciertos códigos de estado HTTP                | gobuster dir -u https://example.com -w wordlist.txt -b 404                              |
| `-H`             | Añade Headers personalizados                          | gobuster dir -u https://example.com -w wordlist.txt -H "Authorization: Bearer token123" |
| `-c`             | Permite enviar una cookie personalizada               | gobuster dir -u https://example.com -w wordlist.txt -c "session=abc123"                 |
| `-k`             | Omite la verificación de SSL                          | gobuster dir -u https://example.com -w wordlist.txt -k                                  |
| `-o`             | Guarda los resultados en un archivo                   | gobuster dir -u https://example.com -w wordlist.txt -o results.txt                      |
| `-q`             | Modo silencioso                                       |                                                                                         |
| `-v`             | Modo verbose                                          |                                                                                         |
| `-d`             | Modo DNS (enumeración de subdominios)                 | gobuster dns -d example.com -w wordlist.txt                                             |
| `-r`             | Activa seguimiento de redirecciones                   | gobuster dns -d example.com -w wordlist.txt -r 8.8.8.8                                  |
| `--wildcard`     | Modo DNS (procesa dominios con entradas wildcard)$^2$ | gobuster dns -d example.com -w wordlist.txt --wildcard                                  |
| `-l`             | Muestra el tamaño de las respuestas                   | gobuster dir -u https://example.com -w wordlist.txt -l                                  |
| `--xl`           | Excluye respuestas de un tamaño específico            | gobuster dir -u https://example.com -w wordlist.txt --exclude-length 9265               |
| `--useragent`    | Definir el user agent                                 |                                                                                         |
| `--random-agent` | Utiliza un valor UA random                            |                                                                                         |
|                  |                                                       |                                                                                         |
	1 --> **Especifica un servidor DNS personalizado** para resolver subdominios durante la enumeración en el modo dns de Gobuster. Por defecto, Gobuster utiliza los servidores DNS configurados en el sistema operativo, pero este parámetro permite definir un servidor específico (como un DNS público o uno controlado por el usuario) para mejorar la precisión o evitar restricciones locales.
	**Cómo funciona**: Gobuster envía consultas DNS al servidor especificado para resolver cada subdominio de la lista de palabras. Esto es útil en entornos donde los servidores DNS predeterminados pueden estar bloqueados, filtrados o no resuelven correctamente ciertos dominios.
	**Casos de uso:**
	Usar servidores DNS confiables (ej. Google 8.8.8.8, Cloudflare 1.1.1.1) para garantizar respuestas fiables.
	Evitar restricciones de red corporativa que filtran consultas DNS.
	Probar subdominios en entornos con configuraciones DNS específicas.
	2 --> **Habilita el manejo de respuestas wildcard** en la enumeración de subdominios. Algunos dominios están configurados con registros DNS wildcard, que resuelven cualquier subdominio (ej. wildcard.example.com) a una IP, generando falsos positivos. Este parámetro detecta estas configuraciones y filtra respuestas irrelevantes para mostrar solo subdominios válidos.
	**Cómo funciona:** Gobuster genera una consulta DNS inicial con un subdominio aleatorio (ej. random123.example.com) para verificar si el dominio usa wildcard. Si detecta una respuesta wildcard, ajusta el escaneo para ignorar resultados que coincidan con la IP o respuesta del wildcard, enfocándose en subdominios reales.
	**Casos de uso:**
	Identificar subdominios válidos en dominios con configuraciones wildcard (común en grandes organizaciones).
	Reducir ruido en auditorías de bug bounties al filtrar falsos positivos.
	Asegurar precisión en entornos con DNS dinámicos o CDNs (ej. Cloudflare, Akamai).
## Cheats
```
php,html,db,conf,config,txt,js,py,bak,conf,env,jar,csv,xml,md,pdf,zip,rar,jpg,jpeg,png,db,sql,log,json
```