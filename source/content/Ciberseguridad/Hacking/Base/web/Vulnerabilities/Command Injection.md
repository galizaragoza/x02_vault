### ¿Qué es Command Injection?
El command injection es una vulnerabilidad que acontece cuando un atacante es capaz de ejecutar comandos en un sistema a través de una aplicación vulnerable. Esta vulnerabilidad se origina en inputs del lado del usuario que no están correctamente sanitizados y son procesados por el sistema que está sirviendo dicha aplicación de manera no segura. El peligro reside en que el atacante puede ejecutar cualquier comando el sistema.
```php
<?php
    $ip = $_GET['ip'];
    system("ping -c 4 " . $ip);
?>
```
En este ejemplo, el script de PHP utiliza la función system() para pingear la IP introducida por el usuario, el problema con este código es que system() pasa directamente el input al intérprete de comandos del sistema, por lo que el usuario podría introducir un input con algo como: `10.10.10.10; whoami` y el sistema interpretaría la totalidad del comando, dando en el output la respuesta no solo al ping, si no también al 
`whoami`. Esto da pie a la exfiltración de archivos de contraseñas como /etc/passwd, SSH keys o incluso a la ejecución de una reverse shell.
##### Cómo hallar esta vulnerabilidad
La idea a la hora de hallar y explotar esta vulnerabilidad es encadenar comandos o "escapar" en el input que está mandando órdenes al sistema de forma insegura, por ejemplo:
```php
command1; command2   # Execute command1 and then command2
command1 && command2 # Execute command2 only if command1 succeeds
command1 || command2 # Execute command2 only if command1 fails
command1 & command2  # Execute command1 in the background
command1 | command2  # Pipe the output of command1 into command2
```
o simplemente, escapar utilizando esos mismos caracteres, con inputs como: `;whoami`, `&& whoami`, `|| whoami`, `& whoami` o `| whoami`. Aún si todo esto fallase, es posible que aún así la aplicación sea vulnerable, y tenga una sanitización mínima pero insuficiente, para eso emplearíamos distintos métodos de bypassing(enconding, ofusación, caracteres alternativos...) que se pueden encontrar en los recursos de abajo.
### Recursos
[Filter Bypasses de PayLOadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#filter-bypasses)
[Command injection payload list](https://github.com/payloadbox/command-injection-payload-list)
[Commix - Herramienta automatizada de CI](https://github.com/commixproject/commix)
### Artículos