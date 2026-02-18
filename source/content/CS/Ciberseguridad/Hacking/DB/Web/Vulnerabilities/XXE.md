### ¿Qué es XXE?
Un ataque XML External Entity(Entidad Externa XML) se da contra aplicaciones que procesan datos en formato XML$^1$, una falta de sanitización propiamente aplicada permite al atacante inyectar entidades XML maliciosas a la aplicación. La mala configuración del *parser*$^2$ o el uso de uno anticuado procesará los input XML externos.
Esto puede llevar a ataques de carácter gravísimo como DoS([[DoS & DDoS]]), [[SSRF]], pivote dentro del perímetro, RCE o exfiltración.
> 1 --> XML es un lenguaje con sintaxis muy similar a de HTML, de hecho sus siglas vienen de Extensible Markup Language, muy similar a HyperText Markup Language, la diferencia principal es que no está predefinido, es decir, es el programador quien marca sus propias etiquetas.
> Evidentemente debe cumplir unas normas mínimas de sintaxis, y se usa principalmente para transferencia de datos, aplicaciones web y documentación.
```XML
<message>
    <warning>
         Hola, mundo
    </warning>
</message>
```
> 2 --> Un parser (analizador sintáctico) es una herramienta que interpreta un código dado, lo analiza y genera un output estructurado
##### Ejemplo de Ataque
Los archivos XML pueden contener Document Type Definitions(DTD) que permite definir y consumir entidades XML, se puede manipular el parser para acceder a archivos sensibles mediante URIs en peticiones maliciosas.
```xml
<?xml version="1.0"?>
<!DOCTYPE data [
<!ELEMENT data (#ANY)>
<!ENTITY file SYSTEM "file:///etc/passwd">
]>
<data>&file;</data>
```
Si el parser esta configurado para procesar entidades externas, en la respuesta nos devolverá el contenido de `/etc/passwd`.
##### Posibilidades
1. **Lectura de archivos**: Mediante la manipulación de las solicitudes, definiendo entidades externas en el DTD solicitando la ruta del archivo
2. **XXE para hacer SSRF**: Es posible aprovechar una vulnerabilidad XXE para engañar al servidor y que este haga peticiones a URLs maliciosas, se define una entidad externa con dicha URL. A veces se podrá realizar tal ataque, solo que no se recibirá una respuesta con y el atacante deberá operar a ciegas.
3. **Blind XXE para exfiltrar información**:
##### Prevención y mitigación
### Recursos
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