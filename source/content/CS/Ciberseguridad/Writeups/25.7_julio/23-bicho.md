# Info
## IP Víctima
```
172.17.0.2
```
# Writeup
## Recon
Un escaneo de puertos revela un servidor HTTP levantado en el puerto 80 mediante Apache 2.4.58. Configuro el /etc/hosts para que redirija correctamente a la URL bicho.dl y me encuentro una página de propaganda de turismo de Suazilandia, la página principal tiene un par de buscadores y un único post, "Bienvenidos a Suzalizandia".
En el post en cuestión hay un texto hablando sobre el destino y una sección de comentarios, corro un WhatWeb y veo que la página es un Wordpress 6.6.2. Lanzamos un wpscan y encontramos un usuario: "bicho", al cual lanzamos un ataque diccionario
## Exploit
## Post