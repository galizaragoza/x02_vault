
```
nuclei -u target -t templates/
```

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-u, --target URL/IP`|Objetivo único|`nuclei -u https://target.com`|
|`-l, --list file`|Lista de objetivos|`nuclei -l targets.txt`|
|`-t, --templates dir`|Directorio o template específico|`nuclei -t cves/ -t misconfig/`|
|`-tags tag1,tag2`|Ejecutar solo templates con esos tags|`nuclei -tags cve,xss`|
|`-severity critical,high`|Filtrar por severidad|`nuclei -severity critical,high`|
|`-c, --concurrency N`|Templates concurrentes (default 25)|`nuclei -c 150`|
|`-rl, --rate-limit N`|Requests por segundo por host (default 150)|`nuclei -rl 400`|
|`-bulk-size N`|Targets por batch (default 25)|`nuclei -bulk-size 50`|
|`-o, --output file`|Guardar resultados|`nuclei -o results.txt`|
|`-jsonl`|Salida JSONL|`nuclei -jsonl -o result.jsonl`|
|`-silent`|Solo mostrar vulnerabilidades encontradas|`nuclei -silent`|
|`-nc, --no-color`|Sin colores|`nuclei -nc`|
|`-update-templates`|Actualizar templates oficiales|`nuclei -update-templates`|
|`-p, --proxy URL`|Proxy HTTP/SOCKS5|`nuclei -p http://127.0.0.1:8080`|
|`-v`|Verbose|`nuclei -v`|
