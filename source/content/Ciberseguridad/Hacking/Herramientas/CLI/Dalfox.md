Dalfox es una herramienta utilizada para analizar y detectar de forma automática vulnerabilidades XSS

|Opción|Función principal|Ejemplo práctico|
|---|---|---|
|`URL` (arg principal)|URL objetivo para escanear (soporta inyección directa o parámetros)|`dalfox url "http://ejemplo.com/search?q=@xss"`|
|`-b, --blind string`|URL de callback para XSS ciego (sin feedback directo)|`-b "https://tu-callback.com"`|
|`-p, --param strings`|Parámetros específicos a probar por XSS|`-p "q" -p "search"`|
|`--only-discovery`|Solo análisis de parámetros, sin escaneo XSS|`--only-discovery`|
|`--skip-discovery`|Salta descubrimiento de params y escanea solo especificados|`--skip-discovery -p "q"`|
|`-o, --output string`|Guarda resultados en archivo|`-o resultados.txt`|
|`--format string`|Formato de salida: plain, json, jsonl|`--format json`|
|`--report`|Muestra reporte detallado al final|`--report`|
|`--custom-payload string`|Carga payloads XSS personalizados de archivo|`--custom-payload "mis-payloads.txt"`|
|`-w, --worker int`|Número de workers concurrentes (default: 100)|`-w 50`|
|`--delay int`|Retraso en ms entre requests (para evadir WAF/rate limits)|`--delay 500`|
|`--proxy string`|Proxy para enrutar requests|`--proxy "http://127.0.0.1:8080"`|
|`--debug`|Modo debug con logs completos|`--debug`|