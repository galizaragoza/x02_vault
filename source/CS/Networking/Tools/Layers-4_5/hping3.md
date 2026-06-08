Herramienta avanzada para crear paquetes TCP/IP custom y analizarlos, permite la creación de paquetes custom para auditar redes o firewalls.

```
hping3 [opciones] host [payload]
```

|Parámetro|Función|
|---|---|
|`-c N`|Número de paquetes a enviar|
|`-i uN`|Intervalo en microsegundos (e.g. -i u1000 = 1000 pps)|
|`--fast`|10 paquetes por segundo|
|`--faster`|100 pps|
|`--flood`|Envío lo más rápido posible (DoS)|
|`-0`|Modo RAW (IP sin protocolo)|
|`-1`|Modo ICMP (ping avanzado)|
|`-2`|Modo UDP|
|`-8`|Modo scan UDP (flood ports)|
|`-9`|Modo listen (responder payloads)|
|`-a IP`|Spoofing de IP origen|
|`-S`|Flag SYN (TCP scan)|
|`-A`|Flag ACK|
|`-R`|Flag RST|
|`-F`|Flag FIN|
|`-P`|Flag PUSH|
|`-U`|Flag URG|
|`-p PORT`|Puerto destino|
|`++ -p`|Incrementa puerto cada paquete (port sweep)|
|`--traceroute`|Traceroute con TCP/UDP/ICMP|
|`-d N`|Tamaño de datos (payload)|
|`-E file`|Usar archivo como payload (exfiltración/túnel)|
|`-w N`|Tamaño ventana TCP|
