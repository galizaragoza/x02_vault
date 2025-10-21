# Info
## IP Víctima
```
172.17.0.2
```
## Info

# Writeup
## Recon
### Nmap
Lanzamos el siguiente comando para iniciar el reconocimiento identificando los puertos abiertos de la máquina víctima:
``` bash
nmap -p 0-65535 --open -T5 -A -sT -Pn -n 172.17.0.2 -oX nmap_TCP.xml && xsltproc nmap_TCP.xml -o nmap_TCP.html && open nmap_TCP.html &>/dev/null & disown
```
Es un escaneo de todos los puertos abiertos(`-p 0-65535 --open`), con agresividad alta e identificando versiones (`-T5 -A`) vía TCP con 3-way handshake (`-sT`), omitiendo el ping inicial ya que sabemos que está activo y deshabilitando la traducción DNS (`-Pn -n`). Dicho escaneo revela un puerto abierto, el 80, por lo que estamos ante un un servidor web, vamos a echar un vistazo a la página.
### Web
Tras un vistazo rápido vemos que la página consiste básicamente en una galería de imágenes expuestas, vamos a fuzzear la página para ver si encontramos alguna ruta que pueda resultar interesante.
El fuzzing revela un directorio /gallery, que a su vez contiene un enlace a un directorio /uploads, en el cual tenemos /images y un handler.php, que presupongo que es lo que permite la subida de archivos a la web, si no está securizado se podría comprometer la web mediante file inclusion.
## Exploitation
### Revshell
Después de revisar el código fuente veo que el formulario de subida no está correctamente securizado, así que subo una revshell en php y me pongo en escucha, ahora, desde /uploads puedo ver el archivo, y al clicar en él estamos dentro como www-data.
## Escalation
En primer lugar sanitizo la shell y lo siguiente es correr un `sudo -l` que revela que puedo correr como sudo siendo www-data/gallery con nano; 