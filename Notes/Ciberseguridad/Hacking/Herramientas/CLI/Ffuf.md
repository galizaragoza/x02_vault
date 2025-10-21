> Ffuf es una herramienta de fuzzing web escrita en Go para llevar a cabo tareas de reconocimiento como el descubrimiento de recursos, subdominios y directorios ocultos, así como ataques de diccionario.
### **Funciones principales**
1. **Descubrimiento de directorios y archivos**: Encuentra directorios y archivos ocultos usando listas de palabras.
2. **Fuzzing de parámetros**: Prueba parámetros de URL, cabeceras o datos POST para vulnerabilidades.
3. **Enumeración de hosts virtuales**: Detecta subdominios o hosts virtuales sin registros DNS.
4. **Filtrado personalizable**: Filtra respuestas por código de estado, tamaño o contenido.
5. **Escaneo recursivo**: Explora subdirectorios descubiertos automáticamente.
### **Usos comunes en pentesting y ciberseguridad**
- **Reconocimiento**: Identifica endpoints ocultos, paneles de administración o archivos sensibles.
- **Descubrimiento de vulnerabilidades**: Prueba inyecciones SQL, XSS o configuraciones débiles.
- **Bug bounties**: Encuentra recursos no listados o endpoints de APIs.
- **Fuerza bruta**: Testea formularios de login o parámetros con listas de credenciales.
- **Integración**: Combina con Burp Suite o SecLists para pruebas avanzadas.
```
ffuf -u <URL> -w <lista_palabras> [opciones]
```

|                         |                                                                                                                                                                       |                                                                                                      |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| -u                      | indica la URL víctima del fuzzing, la palabra clave FUZZ se sitúa en la parte de la URL donde queremos inyectar                                                       | ffuf -u https://target.com/FUZZ -w lista.txt                                                         |
| -w                      | especifica la ruta al wordlist(diccionario) que contiene los valores a probar durante el fuzzing                                                                      | ffuf -u https://target.com/FUZZ -w /ruta/a/lista.txt                                                 |
| -e                      | Especifica las extensiones de archivo a buscar durante el fuzzing (.php, .txt, .html, etc)                                                                            | ffuf -u https://target.com/FUZZ -w lista.txt -e .php,.txt                                            |
| -c                      | activa la salida en colores, para ver mejor los resultados                                                                                                            |                                                                                                      |
| -r                      | Activa el seguimiento de redirecciones                                                                                                                                | ffuf -u https://target.com/FUZZ -w lista.txt -r                                                      |
| -recursion              | Activa el escaneo recursivo de subdirectorios                                                                                                                         | ffuf -u https://target.com/FUZZ -w lista.txt -recursion                                              |
| -recursion-depth        | Define la profundidad máxima de la recursión                                                                                                                          | ffuf -u https://target.com/FUZZ -w lista.txt -recursion -recursion-depth 2                           |
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
|                         |                                                                                                                                                                       |                                                                                                      |
|                         |                                                                                                                                                                       |                                                                                                      |
	1 --> Los modos disponibles en -mode explicados:
	**Clusterbomb**:
    - **Función**: Prueba todas las combinaciones posibles entre listas (producto cartesiano).
    - **Ejemplo**: ffuf -u https://target.com/?user=FUZZ&pass=WFUZZ -w users.txt:FUZZ -w pass.txt:WFUZZ -mode clusterbomb
    - **Uso**: Fuerza bruta exhaustiva (ej. todos los usuarios con todas las contraseñas).
    - **Nota**: Máxima cobertura, pero lento con listas grandes.
    **Pitchfork**:
    - **Función**: Combina listas en paralelo, usando el mismo índice (1º con 1º, 2º con 2º, etc.).
    - **Ejemplo**: ffuf -u https://target.com/?user=FUZZ&pass=WFUZZ -w users.txt:FUZZ -w pass.txt:WFUZZ -mode pitchfork
    - **Uso**: Pruebas de pares predefinidos (ej. usuario-contraseña alineados).
    - **Nota**: Rápido, pero requiere listas del mismo tamaño.
	**Sniper**:
    - **Función**: Fuzzea una posición a la vez, manteniendo las demás fijas (una lista por vez).
    - **Ejemplo**: ffuf -u https://target.com/?user=FUZZ&pass=admin -w users.txt:FUZZ -mode sniper
    - **Uso**: Pruebas específicas de un parámetro (ej. solo usuarios con contraseña fija).
    - **Nota**: Preciso y eficiente para pruebas focalizadas.
## Cheats
```
.php,.html,.db,.conf,.config,.txt,.js,.py,.bak,.conf,.env,.jar,.csv,.xml,.md,.pdf,.zip,.rar,.jpg,.jpeg,.png,.db,.sql,.log,.json
```
Lista default de extensiones para fuzzear
```
./ffuf_basicauth.sh usernames.txt passwords.txt |ffuf -w -:AUTH -u http://172.17.0.2:8080/manager/html -H "Authorization: Basic AUTH" -fc 403,401 -c
```
**Requiere descargar** el [script](https://github.com/ffuf/ffuf-scripts/blob/master/README.md), codifica en base64 todas las combinaciones posibles de las listas seleccionadas y fuzzea por el Basic Auth hasta dar con la clave.
```shell
ffuf -u "http://vulnerable/index.php?page=FUZZ" -w <ruta al diccionario> -c -recursion -recursion-depth 2 -t 64
```
Template para fuzzear en busca de URLs vulnerables a LFI