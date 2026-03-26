| **Parámetro**       | **Función**                                   | **Ejemplo de Sintaxis**           |
| ------------------- | --------------------------------------------- | --------------------------------- |
| `[host]`            | Destino (IP o Dominio).                       | `traceroute google.com`           |
| `-n`                | No resuelve nombres DNS (muestra solo IPs).   | `traceroute -n 8.8.8.8`           |
| `-m` / `--max-hops` | Número máximo de saltos (default 30).         | `traceroute -m 15 google.com`     |
| `-w` / `--wait`     | Segundos de espera para una respuesta.        | `traceroute -w 5 google.com`      |
| `-q` / `--queries`  | Número de paquetes de prueba por salto.       | `traceroute -q 5 google.com`      |
| `-I` / `--icmp`     | Usa peticiones ICMP ECHO en lugar de UDP.     | `traceroute -I google.com`        |
| `-T` / `--tcp`      | Usa paquetes TCP SYN (útil contra firewalls). | `traceroute -T google.com`        |
| `-p` / `--port`     | Especifica el puerto de destino.              | `traceroute -T -p 443 google.com` |