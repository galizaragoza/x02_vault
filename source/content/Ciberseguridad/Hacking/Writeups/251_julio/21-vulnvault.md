# Info 
## IP Víctima
```
172.17.0.2
```
## Credenciales
```
samara
```

# Writeup
## Recon
Un Nmap por TCP revela un servicio ssh en su puerto default y un servidor HTTP mediante Apache 2.4.58 con un título que lee: Generador de Reportes - Centro de Operaciones
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 f5:4f:86:a5:d6:14:16:67:8a:8e:b6:b6:4a:1d:e7:1f (ECDSA)
|_  256 e6:86:46:85:03:d2:99:70:99:aa:70:53:40:5d:90:60 (ED25519)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
|_http-server-header: Apache/2.4.58 (Ubuntu)
|_http-title: Generador de Reportes - Centro de Operaciones
```
Una visita a la página web revela un formulario que permite subir reportes, con los campos de nombre de archivo y fecha. Por otro lado, hay una ruta /upload que permite la subida de archivos. Tras comprobar con BurpSuite que la subida de archivos está rota y no funciona estudiando las respuestas, pruebo varios métodos de escape de Command Injection hasta que puedo ejecutar comandos básicos [[Command Injection]]
## Explotación
Una vez puedo ejecutar comandos básicos desde el input de texto miro /etc/passwd y encuentro al usuario samira, pruebo un ataque diccionario que no funciona al puerto 22 (ssh) y al fallar este miro en /home/samira.
Allí encuentro un id_rsa, al que accedo con un cat y utilizo para entrar mediante ssh como Samira.
## Post explotación
