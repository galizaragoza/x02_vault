# Info
## IP Víctima

```
172.17.0.2
```

## Credenciales

# Writeup

### Escaneo Nmap comprobando servicios, versiones y puertos abiertos
Lanzamos un escaneo `nmap -p 0-65535 --open --min-rate=5000 -A -T5 -sT -Pn -n 172.17.0.2` por TCP con 3-way handshake mostrando las versiones de manera agresiva (T5) omitiendo el ping y la traducción DNS.
``` bash
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey: 
|   256 dc:98:72:d5:05:7e:7a:c0:14:df:29:a1:0e:3d:05:ba (ECDSA)
|_  256 39:42:28:c9:c8:fa:05:de:89:e6:37:62:4d:8b:f3:63 (ED25519)
80/tcp   open  http    Apache httpd 2.4.59 ((Debian))
|_http-server-header: Apache/2.4.59 (Debian)
|_http-title: Apache2 Debian Default Page: It works
8089/tcp open  http    Werkzeug httpd 2.2.2 (Python 3.11.2)
|_http-server-header: Werkzeug/2.2.2 Python/3.11.2
|_http-title: Dale duro bro
```
El escaneo revela un OpenSSH en el puerto por defecto (22), un servidor HTTP con Apache 2.4.59 y otro servicio HTTP con Werkzeug 2.2.2
### Visita a ExploitDB
Investigando sobre el servicio Werkzeug encuentro un exploit que nos debería dar una shell de python. Usando metasploit defino las opciones para tratar de explotar la vulnerabilidad en cuestión, el exploit falla y después de investigar un poco más veo que la versión 2.2.2 tiene vulnerabilidades que no sé si podrán ser explotadas

### Tutorial
Después de un rato me doy cuenta de que el puerto 8089 también esta dando un HTTP que no estaba mirando, una vez en la página me doy cuenta que el sitio es vulnerable a SSTI, después de probar durante un rato no consigo hacer la revshell por lo que me rindo y termino el lab con un tutorial.

### Conclusión
Estaba poniendo mal el parámetro de la URL para dar pie a la revshell, ya he aprendido y guardado la plantilla para que no vuelva a fallar en esto, la escalada de privilegios se hace mediante base64 que tiene permisos para leer cualquier archivo, extraemos el id_rsa y sacamos el hash con ssh2john, después crackeamos la passphrase de nuevo con john y un diccionario, una vez conocida la passphrase podemos entrar directamente como root.