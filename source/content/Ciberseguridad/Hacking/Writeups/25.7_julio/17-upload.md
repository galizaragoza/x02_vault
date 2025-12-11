# Info
## IP Víctima
```
172.17.0.2
```
# Writeup
## Escaneo Nmap
Lanzamos un escaneo Nmap, revelando la siguiente información:
```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Upload here your file
|_http-server-header: Apache/2.4.52 (Ubuntu)
MAC Address: 02:42:AC:11:00:02 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 1 hop

```
El puerto 80 esta abierto corriendo un servidor HTTP con Apache 2.4.52, una búsqueda rápida en ExploitDB revela que no hay exploits para abusar. 
## Visita a la web
Un vistazo rápido a la web nos muestra un selector de archivos que nos permite subir un archivo. Pruebo a poner una plantilla de SSTI en un .txt y subirlo pero no veo ningún cambio, así que voy a lanzar un Gobuster a ver si revela algo interesante. El escaneo revela un dir /uploads donde quedan listados los archivos subidos, y allí esta mi test.txt.
Después de un buen rato trasteando llego a la conclusión después de un par de artículos sobre file upload de que la solución más obvia es esa. Después de varios exploit fallidos por no entender bien como funciona y como se esta procesando luego el texto llego a la revshell de pentest monkey ( [[php-rev-shell]] ) y subo el archivo a través del form. Veo que mi consola sigue en escucha y entonces entiendo que tengo que abrir el archivo desde /uploads para que la revshell conecte (porque el código se procesa en ese momento), y así lo hago.
## Post explotación
Al entrar lo primero que hago es correr `sudo -l` y el output es el siguiente:
``` bash
sudo -l
Matching Defaults entries for www-data on b3284eb7af4a:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User www-data may run the following commands on b3284eb7af4a:
    (root) NOPASSWD: /usr/bin/env

```
Consultando en GTFObins encuentro que la vulnerabilidad es totalmente crítica y simplemente ejecuto `sudo env /bin/sh` inmediatamente ganando acceso a una shell con privilegios de root.