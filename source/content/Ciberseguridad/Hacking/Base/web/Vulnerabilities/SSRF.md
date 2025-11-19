## ¿Qué es Server Side Request Forgery?
Server Side Request Forgery traduciría al castellano como "Falsificación de Petición del Lado del Servidor", y es una vulnerabilidad que permite al atacante realizar peticiones a sitios fuera de lo planeado desde el lado del servidor. Es decir, se "falsifican" las peticiones a un destino con el que los desarrolladores, a menudo uno malicioso del atacante.
### Origen y metodología
La vulnerabilidad SSRF acontece cuando una aplicación web obtiene un recurso remoto a través de input del usuario, sin validar en el proceso dicho input, esto resulta en que el atacante puede manipular las URLs para forzar al servidor a hacer solicitudes HTTP a su antojo, saltándose firewalls, VPNs y ACLs.

Suele desencadenar en explotaciones como el acceso a metadatos de Cloud, la exfiltración de información/documentos, reconocimiento de redes y escaneo de puertos o RCE.

```
url = input("Enter URL:")
response = requests.get(url)
return response
```
Tomando por ejemplo este código que permite al usuario introducir una URL, para que el servidor haga fetch a esta. Si un atacante proporciona un input malicioso como:
```
http://169.254.169.254/latest/meta-data/
```
Podría acceder a información sensible de los metadatos del servicio de Cloud.

![[How-Server-SSRF-works.png]]

El ataque supone un gran riesgo para el resto de la infraestructura de la organización víctima, ya que se puede explotar la confianza que hay en las peticiones del servidor, ya que vienen desde dentro de la propia organización, esto supone un vector de escalada o pivote muy discreto y con poca fricción.
### Tipos de Ataques SSRF
#### Server SSRF Attacks
En un ataque SSRF de servidor el atacante realizará peticiones a la IP 127.0.0.1 o "localhost", provocando que el servidor se haga una petición a sí mismo y se salté controles de autenticación al interpretar que la fuente de la petición es fiable.
Se podría, por ejemplo, realizar una petición a un sitio vulnerable a SSRF a http://localhost/admin, y en muchos casos ganaríamos acceso al panel de control sin necesidad de autenticación.
```
POST /meteorology/forecasts HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 113
weatherApi=http://data.weatherapp.com:8080/meterology/forecasts/check%3FcurrentDateTime%3D6%26cityId%3D1
The attacker can change this to the following:
weatherApi=http://localhost/admin
```
#### Back-end SSRF Attacks
Por otro lado, cuando un ataque esta en una lista confiada con otro componente del back-end, o incluso otra pieza de la infraestructura de la organización, tendremos a través del SSRF un pasaje sin autenticación a través del cual conseguir información o quizá dar con otras vulnerabilidades. La seguridad en estas "zonas" a menudo es mucho menos estricta por entenderse que esta dentro del perímetro. 

![[backend-ssrf.png]]
### Como mitigar SSRF
- Whitelists y resolución DNS
- Responde Handling
- Deshabilitar URL Schemas en desuso
- Autenticación interna
## Recursos
## Fuentes / artículos
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery#methodology)
[PATT bypasses](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery#default-targets)
[PortSwigger](https://portswigger.net/web-security/ssrf)
[Imperva](https://www.imperva.com/learn/application-security/server-side-request-forgery-ssrf/)
