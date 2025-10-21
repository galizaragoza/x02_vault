# ¿Qué es 403 Bypass?
Cuando nos comunicamos mediante HTTP, los servidores que alojan las infraestructuras y páginas se comunican con el lado del cliente mediante códigos de estado, que se envían a modo de respuesta a nuestras solicitudes.
Hay una serie de rangos de códigos de estado que indican distintos tipos de respuesta en base a la 'reacción' del servidor. Por ejemplo, los 200 indican éxito, mientras que los 500 indican error del lado del servidor y los 400 error del lado del cliente.

| Info |     Éxito      | Redirecciones | Client-side error  |     Server-side error     |
| ---- | :------------: | ------------- | :----------------: | :-----------------------: |
| 100  |    200 - OK    | 300           | 400 - Bad Request  |   500 - Internal error    |
|      | 201 - Created  |               | 401 - Unauthorized |   501 - Not implemented   |
|      | 202 - Accepted |               |  403 - Forbidden   |     502 - Bad Gateway     |
|      | 203 - NA info  |               |  404 - Not found   | 503 - Service unavailable |
|      |                |               |        ...         |                           |

Concretamente, el código 403 indica que no estamos autorizados para solicitar ese recurso concreto. No hay que rendirse al llegar a un endpoint que da 403. La naturaleza caótica de las aplicaciones modernas deja muchas brechas abiertas a fallos de autenticación, y se puede llegar a bypassear un error 403.

![[ACproblem.png|705x373]]

## Metodología
### Acceso negado en base a la IP
Si el acceso a cierto recurso se nos está denegando debido a la IP desde la que se solicita dicho recurso, puede utilizarse con una herramienta de interceptación el Header `X-FORWARDED-FOR:127.0.0.1`
```
Referer:
```
### Path normalisation
A la hora de tratar de acceder a un recurso prohibido, se pueden bypassear algunos sistemas haciendo la solicitud así: `http://private/./resource`, en vez de así `http://secure.com/private/resource`. Estamos solicitando el mismo recurso pero se puede bypassear un control de acceso que solo filtra por el string `/private/resource.
Con una lógica similar, si el 403 esta en `http://secure.com/resource`, se podría llegar a acceder haciendo algo como `http://secure.com/whatever/../resource` (esto solo funcionará con curl o en proxies)
### URL encoding
URL encodear el último caracter de la ruta puede resultar exitoso a menudo. `http://secure.com/resource` pasa a ser `http://secure.com/resourc%65`, se pueden aplicar varias capas de encoding o hacer doble encoding.

> Es recomendable escanear en busca de versiones antiguas o buscar por repositorios/legacy (waybackURL...) viejos endpoints o versiones descontinuadas de API que puedan ser accesibles.

## Fuentes
https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/403-and-401-bypasses.html
[Explicación + ejemplos explotación](https://youtu.be/PvpXRBor-Jw?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)