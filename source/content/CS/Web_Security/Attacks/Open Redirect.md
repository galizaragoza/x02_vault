
![[open-redirection.jpeg]]

# Métodos de redirección que pueden explotarse
## Path-based redirects
En los ataques path-based, la lógica de redirección reside en la propia ruta, ya sea con:
- Barras (`/`) en la URL: `https://example.com/redirect/http://malicious.com`
- Inyección de rutas relativas: `https://example.com/redirect/../http://malicious.com`
## Javascript-based redirects
```javascript
var redirectTo = "http://trusted.com";
window.location = redirectTo;
```
Sería explotable por un payload como este: ``?redirectTo=http://malicious.com``
Esta es una lista de algunos parámetros habituales con los que nombrar a la variable que gestiona las redirecciones:
```
?checkout_url={url}
?continue={url}
?dest={url}
?destination={url}
?go={url}
?image_url={url}
?next={url}
?redir={url}
?redirect_uri={url}
?redirect_url={url}
?redirect={url}
?return_path={url}
?return_to={url}
?return={url}
?returnTo={url}
?rurl={url}
?target={url}
?url={url}
?view={url}
/{url}
/redirect/{url}
```
### Bypasses
Puede llegar a darse el caso de que haya alguna validación o securización funcional en la aplicación pero esta sea insuficiente, estas son algunas técnicas para burlar dichos controles.
- Usar un dominio o palabra whitelisted (que esté en la lista de permitidos de la aplicación)
- Usar CRLF$¹$ para bypassear palabras baneadas
```
java%0d%0ascript%0d%0a:alert(0)
```
- Usar `//` o `////` para bypassear la palabra baneada "`http`"
- Usar `https:` para bypassear "`//`"
- Usar `\/\/` para bypassear "`//`"
- Usar `%E3%80%82` para bypassear "`.`"
- Utilizar un byte nulo (`%00`)

# Artículos
[Lab Wallarm](https://lab.wallarm.com/what/vulnerabilidad-de-redireccionamiento-abierto/?lang=es)
[PortSwigger](https://portswigger.net/kb/issues/00500100_open-redirection-reflected)
[Curiosidades de Hackers](https://curiosidadesdehackers.com/vulnerabilidad-open-redirect-ejemplos-y-soluciones/)
[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Open%20Redirect)
