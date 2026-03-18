**URLCrazy** es una herramienta de OSINT diseñada para detectar y generar variantes de dominios para ataques de _typosquatting_, _phishing_ y espionaje corporativo.

|**Parámetro**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`-k`|**Keyboard**: Genera variantes basadas en errores de teclado (teclas adyacentes).|`urlcrazy -k google.com`|
|`-s`|**Similarity**: Genera variantes basadas en caracteres visualmente similares (homoglifos).|`urlcrazy -s google.com`|
|`-r`|**Resolve**: Intenta resolver las direcciones IP de los dominios generados.|`urlcrazy -r example.com`|
|`-i`|**Invalid**: Muestra solo los dominios que no tienen una IP válida (libres).|`urlcrazy -i example.com`|
|`-p`|**Popularity**: Comprueba la popularidad del dominio en motores de búsqueda.|`urlcrazy -p example.com`|
|`-f`|**Format**: Define el formato de salida (human, csv, json).|`urlcrazy -f csv example.com`|
|`-o`|**Output**: Guarda los resultados en un archivo específico.|`urlcrazy -o resultados.csv example.com`|
|`-n`|**No-resolve**: Desactiva la resolución DNS para aumentar la velocidad.|`urlcrazy -n example.com`|1