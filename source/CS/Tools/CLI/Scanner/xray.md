xray es un escáner automático que evalúa la seguridad de un sitio web

```bash
./xray webscan --listen 127.0.0.1:7777 --html-output result.html
```

|Parámetro|Función|
|---|---|
|`webscan`|Modo escaneo web (principal)|
|`--listen IP:PORT`|Servidor listener para recibir URLs (e.g., 127.0.0.1:7777)|
|`--basic-crawler`|Crawler básico para enumerar endpoints|
|`--network-crawler`|Crawler de red (requiere config)|
|`--html-output file`|Salida en HTML|
|`--text-output file`|Salida en texto|
|`--json-output file`|Salida en JSON|
|`--basic-crawler.max-depth N`|Profundidad máxima crawler (default 3)|
|`--concurrency N`|Concurrencia de escaneo (default 50)|
|`--plugins-dir dir`|Directorio de plugins/POCs personalizados|
|`--server-ip IP`|IP para conexiones inversas en exploits|
|`--debug`|Modo debug (logs detallados)|
|`-h`|Ayuda|