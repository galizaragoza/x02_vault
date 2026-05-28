FFUF es una herramienta de fuzzing web escrita en Go para llevar a cabo tareas de reconocimiento como el descubrimiento de recursos, subdominios y directorios ocultos, así como ataques de diccionario.

```
ffuf -u <URL> -w <lista_palabras> [opciones]
```

# Params

|                         |                                                                                                                                                                       |                                                                                                      |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| -u                      | indica la URL víctima del fuzzing, la palabra clave FUZZ se sitúa en la parte de la URL donde queremos inyectar                                                       | ffuf -u https://target.com/FUZZ -w lista.txt                                                         |
| -w                      | especifica la ruta al wordlist(diccionario) que contiene los valores a probar durante el fuzzing                                                                      | ffuf -u https://target.com/FUZZ -w /ruta/a/lista.txt                                                 |
| -e                      | Especifica las extensiones de archivo a buscar durante el fuzzing (.php, .txt, .html, etc)                                                                            | ffuf -u https://target.com/FUZZ -w lista.txt -e .php,.txt                                            |
| -c                      | activa la salida en colores, para ver mejor los resultados                                                                                                            |                                                                                                      |
| -r                      | Activa el seguimiento de redirecciones                                                                                                                                | ffuf -u https://target.com/FUZZ -w lista.txt -r                                                      |
| -recursion              | Activa el escaneo recursivo de subdirectorios                                                                                                                         | ffuf -u https://target.com/FUZZ -w lista.txt -recursion                                              |
| -recursion-depth        | Define la profundidad máxima de la recursión                                                                                                                          | ffuf -u https://target.com/FUZZ -w lista.txt -recursion -recursion-depth 2                           |
| -recursion-strategy     | Default, greedy                                                                                                                                                       |                                                                                                      |
| -p                      | Marca el delay entre solicitudes (en segundos)                                                                                                                        | ffuf -u https://target.com/FUZZ -w lista.txt -p 0.1                                                  |
| -mc                     | Activa la salida en colores filtrado por estado HTTP                                                                                                                  | ffuf -u https://target.com/FUZZ -w lista.txt -mc 200,301,302<br>                                     |
| -t                      | Especifica la cantidad de hilos a utilizar en el proceso (**nunca** usar mas de 200)                                                                                  |                                                                                                      |
| -fc [estados a excluir] | Excluye los estados HTTP no deseados                                                                                                                                  | ffuf -u https://target.com/FUZZ -w lista.txt -fc 404                                                 |
| -fs [número de bytes]   | Filtra en base al tamaño en bytes                                                                                                                                     | ffuf -u https://target.com/FUZZ -w lista.txt -fs 4242                                                |
| -fr                     | Filtra por contenido                                                                                                                                                  | ffuf -u https://target.com/FUZZ -w lista.txt -fr "Page Not Found"                                    |
| -maxtime                | Limita en segundos la duración del fuzzing                                                                                                                            | ffuf -u https://target.com/FUZZ -w lista.txt -maxtime 300                                            |
| -maxtime-job            | limita en segundos la duración de un cada tarea                                                                                                                       | ffuf -u https://target.com/FUZZ -w lista.txt -maxtime-job 10                                         |
| -ic                     | Ignora los comentarios del diccionario                                                                                                                                | ffuf -u https://target.com/FUZZ -w lista.txt -ic                                                     |
| -s                      | Modo silencioso                                                                                                                                                       | ffuf -u https://target.com/FUZZ -w lista.txt -s                                                      |
| -v                      | Modo verbose                                                                                                                                                          | ffuf -u https://target.com/FUZZ -w lista.txt -v                                                      |
| -request                | Especifica un archivo con una solicitud HTTP cruda para usar como plantilla                                                                                           | ffuf -request request.txt -w lista.txt                                                               |
| -request-proto          | Define el protocolo para la solicitud del archivo -request                                                                                                            | ffuf -request request.txt -request-proto https -w lista.txt                                          |
| -mode                   | Define el modo de fuzzing (clusterbomb, pitchfork, o sniper) (1)                                                                                                      | ffuf -u https://target.com/?user=FUZZ&pass=WFUZZ -w users.txt:FUZZ -w pass.txt:WFUZZ -mode pitchfork |
| -ac                     | Activa la calibración automática para filtrar respuestas dinámicas basadas en un baseline inicial. Ayuda a reducir falsos positivos en sitios con contenido variable. | ffuf -u https://target.com/FUZZ -w lista.txt -ac                                                     |
| -H                      | Añade headers HTTP personalizada (puede usarse +1 vez)                                                                                                                | ffuf -u https://target.com/FUZZ -w lista.txt -H "Authorization: Bearer token123"                     |
| -X                      | Especifica el método HTTP deseado                                                                                                                                     | ffuf -u https://target.com/login -w lista.txt -X POST -d "username=admin&password=FUZZ"              |
| -b                      | Especifica las cookies para la solicitud HTTP. Útil para acceder a recursos autenticados o mantener sesiones.                                                         | ffuf -u https://target.com/FUZZ -w lista.txt -b "session=abc123"                                     |
| -d                      | Define la información a mandar por POST para fuzzing                                                                                                                  | ffuf -u https://target.com/api -w lista.txt -X POST -d "key=FUZZ"                                    |
| -o                      | Guarda el resultado en un archivo                                                                                                                                     | ffuf -u https://target.com/FUZZ -w lista.txt -o resultados.json                                      |
| `-of`                   | Output file format. Available formats: json, ejson, html, md, csv, ecsv                                                                                               |                                                                                                      |
| `-enc`                  | Encoder para las keywords (FUZZ:urlencode b64encode)                                                                                                                  |                                                                                                      |
| `-ac`                   | Calibración automática del filtrado                                                                                                                                   |                                                                                                      |

# Protips
## Fuzzear en varias posiciones
```
ffuf -u "url/function.php/FUZZ?=FUZZ2" -w dict1.txt:FUZZ -w dict2.txt:FUZZ2 
```

## Extensiones fuzzing
### Basic
```
.php,.html,.db,.conf,.config,.txt,.js,.py,.bak,.conf,.env,.xml,.zip,.rar,.jpg,.jpeg,.png,.db,.sql,.log,.json
```
### Llaves
```
.pub,.pem,.cert,.key
```
### Conf y backup
```
.conf,.env,.bak,.old
```
### Bomb
```
.php,.html,.db,.conf,.config,.txt,.js,.py,.bak,.conf,.env,.jar,.csv,.xml,.md,.pdf,.zip,.rar,.jpg,.jpeg,.png,.db,.sql,.log,.json,.aspx,.cfg,.old,.phps,.pub,.pem,.cert,.key
```



## Basic Auth bruteforce
```
./ffuf_basicauth.sh usernames.txt passwords.txt |ffuf -w -:AUTH -u http://172.17.0.2:8080/manager/html -H "Authorization: Basic AUTH" -fc 403,401 -c
```
**Requiere descargar** el [script](https://github.com/ffuf/ffuf-scripts/blob/master/README.md).

 ### URLs vulnerables a LFI
```shell
ffuf -u "http://vulnerable/index.php?page=FUZZ" -w <ruta al diccionario> -c -recursion -recursion-depth 2 -t 64

ffuf -u "http://vulnerable/index.php?FUZZ=FUZZ2" -w /usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -w /usr/share/wordlists/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ2  -ac -t 100 -c -r
```

## Fuzz all domains from `file.txt`
```bash
cat live.txt | xargs -I@ sh -c 'ffuf -w wordlists.txt -u @/FUZZ -mc 200'
```

### [FFUF 4 virtual hosts](https://github.com/ffuf/ffuf?tab=readme-ov-file#virtual-host-discovery-without-dns-records)