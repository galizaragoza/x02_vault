# Info
## IP Víctima
```
172.17.0.2
```
# Writeup
## Recon
Un escaneo de puertos revela un SSH por el puerto default y un servidor HTTP en el 5000 corriendo en Werkzeug 2.2.2 de título "Restaurante Balulero - Inicio".
Visito la web y es una página de un restaurante, echando un vistazo a la página y lanzando un fuzzeo de directorios me encuentro con varias cosas interesantes, /admin da un 302 (redirección a /login) hay un panel /login con un panel corriente y un /pedido con un formulario que pide nombre y plato deseado. También encuentro /carta pero no veo nada interesante, en principio lo intuitivo es probar las securizaciones de ambos formularios.
Paso un rato probando payloads de command injection y XSS en ambos formularios pero no consigo avanzar así que por el momento descarto ambas posibilidades. Un ataque diccionario al panel de login revela que las credenciales de acceso eran admin/admin, con lo que estamos dentro del panel de control.
Inspeccionando el código fuente del panel de admin encontramos un comentario que lee:
<!-- Backup de acceso: sysadmin:backup123 -->
## Exploit
Vamos a probar a conectar por ssh con estas credenciales. Accedemos al ssh sin problemas y haciendo un cat a /etc/passwd encontramos otro usuario, balulero, con el que probamos un ataque diccionario. El ataque diccionario está tardando demasiado así que lo cancelo, mirando con un cat el app.py que hay en /sysadmin encontramos un secreto: 'cuidaditocuidadin', pruebo a acceder por ssh a balulero con esa contraseña y funciona, estamos dentro
## Post
Lanzo un ls -la y me encuentro con .bash_history, me encuentro con lo siguiente:
``` bash
nano ~/.bashrc
apt install nano -y
exit
nano ~/.bashrc
source nano ~/.bashrc
source ~/.bashrc
alias
su root
exit
```
Lanzo el comando alias y me encuentro con lo siguiente alias ls='ls --color=auto'
alias ser-root='echo chocolate2 | su - root'
Solo queda hacer su root con la contraseña chocolate2 y ya somos root.