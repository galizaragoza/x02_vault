### ¿Qué es CSTI?
CSTI o Client Side Template Injection es una vulnerabilidad muy similar a [[SSTI]], la principal diferencia es que SSTI sucede (como su nombre indica) desde el lado del servidor, y CSTI ocurre en el cliente.
Es decir, la lógica es la misma, si el atacante es capaz de inyectar payloads maliciosos en una plantilla vulnerable ese código se ejecutará, solo que en vez de ir al servidor y por tanto darnos acceso a RCE y demás, todo queda en el cliente, por lo que se buscan explotaciones como el robo de cookies.
Incluso el payload para testear es el mismo
```
{{7*7}}
```
### Mitigación y prevención 
### Recursos
[Table de template injection](https://cheatsheet.hackmanit.de/template-injection-table/)
### Fuentes
[PortSwigger Template Injection](https://portswigger.net/research/template-injection)
[HackTricks](https://book.hacktricks.wiki/en/pentesting-web/client-side-template-injection-csti.html#client-side-template-injection-csti)
[CSTI Hackmanit](https://hackmanit.de/en/blog-en/178-template-injection-vulnerabilities-understand-detect-identify/)
