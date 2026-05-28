| **Parámetro/Opción** | **Función**                       | **Ejemplo de Sintaxis**        |
| -------------------- | --------------------------------- | ------------------------------ |
| `-i interfaz`        | Especifica interfaz de red        | `dnsspoof -i eth0`             |
| `-f archivo`         | Archivo con hosts falsificados    | `dnsspoof -f hosts.txt`        |
| `host expresion`     | Filtra por expresión de host      | `dnsspoof host 192.168.1.*`    |
| `-n`                 | No resuelve direcciones a nombres | `dnsspoof -n`                  |
| `-q`                 | Modo silencioso (menos salida)    | `dnsspoof -q`                  |
| `-t`                 | TTL para respuestas DNS           | `dnsspoof -t 3600`             |
| Combinación común    | Intercepta todo el tráfico DNS    | `dnsspoof -i eth0 udp port 53` |