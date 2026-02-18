 # Info
## IP Víctima
```
172.17.0.2
```
# Writeup
## Reconocimiento
Un Nmap revela los puertos 22 y 80 abiertos, el puerto 80 corre un servidor HTTP en Apache 2.4.61.
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)
| ssh-hostkey: 
|   256 08:ba:95:95:10:20:1e:54:19:c3:33:a8:75:dd:f8:4d (ECDSA)
|_  256 1e:22:63:40:c9:b9:c5:6f:c2:09:29:84:6f:e7:0b:76 (ED25519)
80/tcp open  http    Apache httpd 2.4.61 ((Debian))
|_http-server-header: Apache/2.4.61 (Debian)
|_http-title: test page

```
Al visitar la página vemos que hay un formulario de login, así que lanzo un SQLmap a ver que nos dice.
## Explotación
Mientras el SQLmap acaba yo mismo pruebo los campos del login y no están sanitizados, con SQLmap saco todos los usuarios y contraseñas de la tabla users y hago dos diccionarios con los que entro de manera muy sencilla al ssh.
## Post explotación
Ya que no hay sudo, hago un `grep '' /root/pass.hash` que me da un hash md5 que desencripto en una página, dándome la contraseña de root.
