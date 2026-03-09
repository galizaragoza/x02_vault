Envía paquetes ICMP para probar conectividad de red.

| **Parámetro**       | **Función**                   | **Ejemplo**                    |
| --------------- | ------------------------- | -------------------------- |
| `-4`            | Usa solo IPv4.            | `ping -4 example.com`      |
| `-6`            | Usa solo IPv6.            | `ping -6 example.com`      |
| `-c count`      | Envía count paquetes.     | `ping -c 10 example.com`   |
| `-i interval`   | Intervalo entre paquetes. | `ping -i 0.5 example.com`  |
| `-I interface`  | Fuente de interfaz.       | `ping -I eth0 example.com` |
| `-n`            | Salida numérica.          | `ping -n example.com`      |
| `-q`            | Salida quieta.            | `ping -q example.com`      |
| `-s packetsize` | Tamaño de datos.          | `ping -s 100 example.com`  |
| `-w deadline`   | Límite de tiempo.         | `ping -w 5 example.com`    |
| `-W timeout`    | Timeout de respuesta.     | `ping -W 2 example.com`    |