# HTTP / HTTPS
HTTP es un protocolo que utiliza un modelo de request-reponse (solicitud-respuesta), comunica clientes con servidores, los clientes realizan solicitudes por distintos recursos, imágenes, archivos... Y el servidor contesta con una serie de códigos de estado

Algunos ejemplos de HTTP Requests son
1. GET --> Solicita un recurso
2. POST --> "Crea" un recurso
3. PUT --> Modifica un recurso existente
4. DELETE --> Elimina un recurso

Se contesta a estas solicitudes con una serie de códigos de estado, desde los 100 hasta los 500

| Info |     Éxito      | Redirecciones | Client-side error  |     Server-side error     |
| ---- | :------------: | ------------- | :----------------: | :-----------------------: |
| 100  |    200 - OK    | 300           | 400 - Bad Request  |   500 - Internal error    |
|      | 201 - Created  |               | 401 - Unauthorized |   501 - Not implemented   |
|      | 202 - Accepted |               |  403 - Forbidden   |     502 - Bad Gateway     |
|      | 203 - NA info  |               |  404 - Not found   | 503 - Service unavailable |
### HTTPS
HTTPS añade una capa de seguridad a las conexiones tramitadas por HTTP encriptando la información mediante TLS (Transport Layer Security), evitando MiTM attacks y garantiza confidencialidad.
### HTTP3
https://youtu.be/a-sBfyiXysI?list=PL65_wYSEg5HdGgpkBRymedI24hCXWqB6l


# WebSocket
WebSocket es relativamente similar a HTTP/S, la diferencia principal (y la razón de ser de Sockets) es que HTTP se basa en un método de comunicación de solicitud-respuesta, el estado de las comunicaciones en HTTP se mantiene y recuerda mediante sesiones y cookies.

Por otro lado WebSockets habilita un canal de comunicación bidireccional que puede enviar y recibir por ambas partes mensajes sin esperar a la respuesta. 
![[HTTPvsWebSockets.jpg|415x306]]
https://ably.com/topic/websockets




# TCP / UDP
### TCP
Transmission Control Protocol es un protocolo orientado a la conexión, ampliamente usado en muchos otros protocolos y clave para la comunicación íntegra de dos o más equipos. TCP establece una conexión, uno de los ordenadores envía un mensaje SYN (syncronize), el otro cont
esta con un mensaje SYN-ACK(syncronize-acknowledge), por último, el ordenador que inició la conexión envía ACK y se inicia una conexión sobre la cual se transfiere la info. Esto se llama *three way handshake*.

![[3way-handshake.webp|464x199]]

TCP garantiza la llegada íntegra de toda la información de un sistema a otro, ya que realiza comprobaciones en los paquetes que manda y los reenvía si es que alguno falla
#### Modbus TCP
[Modbus TCP](https://www.wut.de/e-57www-04-apes-000.php)


### UDP
UDP también es un protocolo utilizado para el envío de información, sin embargo, es *connection-less*, esto quiere decir que no establece una sesión previo al inicio de la transmisión de datos, por lo que no hace comprobaciones ya que no hay un canal en el cual hacerlas, simplemente envía los paquetes de datos sin preocuparse de si han llegado o no.
Esto lo hace más eficiente para cosas como VoIP, streaming o similares, pero pésimo para transmisiones que requieren integridad y fiabilidad. 

![[tcp-vs-udp.webp|448x448]]


# SSH
![[ssh.png|885x585]]
[How SSH Really Works](https://youtu.be/rlMfRa7vfO8?list=PL65_wYSEg5HdGgpkBRymedI24hCXWqB6l)
[SSH Port Forwarding](https://youtu.be/YlYZtDnZUO8)
[SSH Tunneling](https://youtu.be/5KKP8qPHrP0?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)

# gRPC
https://youtu.be/gnchfOojMk4?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR

# Microsoft protocols
### RDP
RDP o Remote Desktop Protocol es un protocolo desarrollado por Microsoft que permite manipular con normalidad un equipo a distancia (de forma telemática) a través de un canal cifrado. 
Las conexiones se tramitan a través del puerto 3389
[Fortinet RDP](https://www.fortinet.com/lat/resources/cyberglossary/remote-desktop-protocol)
### Active Directory
#### LDAP


## Fuentes


