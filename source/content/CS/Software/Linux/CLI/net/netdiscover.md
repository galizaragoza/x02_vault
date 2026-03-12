**netdiscover** es una herramienta de escaneo de red activa/pasiva basada en peticiones ARP. Es muy útil para descubrir hosts en redes inalámbricas o conmutadas.

| **Parámetro** | **Función**                                             | **Ejemplo de sintaxis**         |
| ------------- | ------------------------------------------------------- | ------------------------------- |
| `-i`          | Especifica la interfaz de red (eth0, wlan0, etc.)       | `netdiscover -i eth0`           |
| `-r`          | Escanea un rango de red específico (formato IP/máscara) | `netdiscover -r 192.168.1.0/24` |
| `-l`          | Lee una lista de rangos de un archivo externo           | `netdiscover -l ranges.txt`     |
| `-p`          | Modo pasivo (solo escucha, no envía paquetes)           | `netdiscover -p`                |
| `-m`          | Escanea una lista de direcciones MAC conocidas          | `netdiscover -m nodes.txt`      |
| `-S`          | Tiempo de espera (en milisegundos) entre peticiones ARP | `netdiscover -S 100`            |
| `-f`          | Habilita el modo de escaneo rápido                      | `netdiscover -f`                |