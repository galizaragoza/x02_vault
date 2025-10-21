## ¿Qué es Open Redirect?

Open Redirect, Open URL Redirect o redirección abierta es, básicamente, cuando una redirección proporcionada por un usuario o dinámica no se valida o filtra como es debido. La vulnerabilidad permite que el atacante confeccione una URL personalizada que desde dentro del propio sitio web reputado o confiado redirija al usuario a un dominio externo arbitrario, se aprovecha de la reputación/confianza de un sitio para llevarte a su sitio malicioso.

`https://sitio-reputado.com/redirect.php?url=http://sitio-malicioso.com

El atacante aprovechará esta vulnerabilidad para redirigir a usuarios que confían en un sitio web a sitios maliciosos en los cuáles pueden ejecutar ataques de robo de credenciales o sesiones, phishing, instalación de malware, o llevar a cabo una acción en nombre de un usuario sin este saberlo.

El origen de esta vulnerabilidad reside en la falta de validación del sitio vulnerable respecto a las redirecciones, es decir, la aplicación no verifica correctamente los parámetros URL de las redirecciones en su propio sitio. En definitiva, es crítico aplicar los principios de Zero Trust y [[Default Deny Policy]] en el desarrollo y desconfiar por defecto de las redirecciones dinámicas.

![[open-redirection.jpeg]]

Entrando al lado técnico, una URL como esta explotaría esta vulnerabilidad en una aplicación que no aplicó las medidas de validación correctas `https://test.com/redirect.php?url=http://attacker.com`. Por ejemplo, el sitio malicioso podría contener un clon del formulario de login de la página legítima original a través del cual se robarían las credenciales del cliente, enviándolas a un servidor del atacante.

### Métodos de redirección que pueden explotarse
#### 1. Path-based redirects (redirecciones basadas en la ruta)
En los ataques path-based, la lógica de redirección reside en la propia ruta, ya sea con:
- Barras (`/`) en la URL: `https://example.com/redirect/http://malicious.com`
- Inyección de rutas relativas: `https://example.com/redirect/../http://malicious.com`
#### 2. Javascript-based redirects (redirecciones basadas en Javascript)
Si la aplicación hace uso de Javascript para gestionar las redirecciones, un atacante puede manipular las variables del script, por ejemplo este código:
```javascript
var redirectTo = "http://trusted.com";
window.location = redirectTo;
```
Sería explotable por un payload como este: ``?redirectTo=http://malicious.com``
Esta es una lista de algunos parámetros habituales con los que nombrar a la variable que gestiona las redirecciones:
```
?checkout_url={payload}
?continue={payload}
?dest={payload}
?destination={payload}
?go={payload}
?image_url={payload}
?next={payload}
?redir={payload}
?redirect_uri={payload}
?redirect_url={payload}
?redirect={payload}
?return_path={payload}
?return_to={payload}
?return={payload}
?returnTo={payload}
?rurl={payload}
?target={payload}
?url={payload}
?view={payload}
/{payload}
/redirect/{payload}
```
### Bypasses
Puede llegar a darse el caso de que haya alguna validación o securización funcional en la aplicación pero esta sea insuficiente, estas son algunas técnicas para burlar dichos controles.
- Usar un dominio o palabra whitelisted (que esté en la lista de permitidos de la aplicación)
- Usar CRLF$¹$ para bypassear palabras baneadas
```
java%0d%0ascript%0d%0a:alert(0)
```
- Usar `//` o `////` para bypassear la palabra baneada "`hhtp`"
- Usar `https:` para bypassear "`//`"
- Usar `\/\/` para bypassear "`//`"
- Usar `%E3%80%82` para bypassear "`.`"
- Utilizar un byte nulo (`%00`)
>1 --> CR y LF son caracteres de control y bytecode. CR (Carriage Return, `\r`, `0x0D` en hexadecimal) mueve el cursor al principio de la línea, sin avanzar a la siguiente. LF (Line Feed, `\n`, `0x0A`), mueve el cursor a la siguiente línea sin volver al principio de la línea. Se combinan para marcar el final de una línea así: `%0D%0A`.
## Recursos

## Artículos
[Lab Wallarm](https://lab.wallarm.com/what/vulnerabilidad-de-redireccionamiento-abierto/?lang=es)
[PortSwigger](https://portswigger.net/kb/issues/00500100_open-redirection-reflected)
[Curiosidades de Hackers](https://curiosidadesdehackers.com/vulnerabilidad-open-redirect-ejemplos-y-soluciones/)
[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Open%20Redirect)
