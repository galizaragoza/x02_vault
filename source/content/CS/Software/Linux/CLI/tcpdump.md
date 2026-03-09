

| **Parámetro**          | **Función**                                | **Sintaxis de Ejemplo**          |
| ------------------ | -------------------------------------- | ---------------------------- |
| `-i interfaz`      | Especificar interfaz de red            | `tcpdump -i eth0`            |
| `-n`               | No resolver nombres DNS                | `tcpdump -n`                 |
| `-nn`              | No resolver nombres ni puertos         | `tcpdump -nn`                |
| `-c num`           | Capturar número específico de paquetes | `tcpdump -c 10`              |
| `-s snaplen`       | Definir tamaño de captura (bytes)      | `tcpdump -s 0`               |
| `-w archivo`       | Guardar captura en archivo             | `tcpdump -w captura.pcap`    |
| `-r archivo`       | Leer captura desde archivo             | `tcpdump -r captura.pcap`    |
| `-v`/`-vv`/`-vvv`  | Niveles de verbosidad                  | `tcpdump -v`                 |
| `-X`               | Mostrar contenido en hex y ASCII       | `tcpdump -X`                 |
| `-A`               | Mostrar contenido en ASCII             | `tcpdump -A`                 |
| `-q`               | Modo silencioso (menos output)         | `tcpdump -q`                 |
| `-e`               | Mostrar encabezado de enlace           | `tcpdump -e`                 |
| `port puerto`      | Filtrar por puerto                     | `tcpdump port 80`            |
| `host direccion`   | Filtrar por dirección IP/host          | `tcpdump host 192.168.1.1`   |
| `net red`          | Filtrar por red                        | `tcpdump net 192.168.1.0/24` |
| `src`/`dst`        | Filtrar por origen/destino             | `tcpdump src 192.168.1.100`  |
| `tcp`/`udp`/`icmp` | Filtrar por protocolo                  | `tcpdump tcp`                |