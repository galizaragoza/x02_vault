**Sublist3r** es una herramienta escrita en Python diseñada para enumerar subdominios de sitios web utilizando motores de búsqueda y servicios públicos como Google, Bing, Baidu y VirusTotal.

|**Parámetro**|**Función**|**Ejemplo de sintaxis**|
|---|---|---|
|`-d`|Especifica el dominio para enumerar subdominios|`sublist3r -d example.com`|
|`-b`|Habilita el módulo de fuerza bruta (usando subbrute)|`sublist3r -d example.com -b`|
|`-p`|Escanea los subdominios encontrados en busca de puertos TCP|`sublist3r -d example.com -p 80,443`|
|`-v`|Modo verbose (muestra resultados en tiempo real)|`sublist3r -v -d example.com`|
|`-t`|Número de hilos para el módulo de fuerza bruta|`sublist3r -d example.com -b -t 50`|
|`-e`|Especifica motores de búsqueda separados por comas|`sublist3r -d example.com -e google,bing`|
|`-o`|Guarda los resultados en un archivo de texto|`sublist3r -d example.com -o out.txt`|