# Info
## IP Víctima
```
172.17.0.2
```
# Writeup
Un humilde intento de documentar mi progreso, y de paso intentar hacer un aporte a la comunidad y ayudar a los que vengan detrás mío tal y como yo recibí muchísima ayuda y conocimiento de los que me enseñan. 
## Recon
En primer lugar comenzaremos por realizar el rutinario escaneo de puertos como en cualquier otra máquina, para ello usaré nmap:
``` shell
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)
| ssh-hostkey: 
|   256 2c:ea:4a:d7:b4:c3:d4:e2:65:29:6c:12:c4:58:c9:49 (ECDSA)
|_  256 a7:a4:a4:2e:3b:c6:0a:e4:ec:bd:46:84:68:02:5d:30 (ED25519)
80/tcp open  http    Apache httpd 2.4.61 ((Debian))
|_http-title: Login Page
|_http-server-header: Apache/2.4.61 (Debian)
```
Del escaneo no sacamos gran cosa más allá de la web que está corriendo en puerto 80 mediante HTTP, así que vamos a echarle un vistazo a dicha página. Una vez dentro podemos ver que no tiene más que un formulario de login, a primera vista, inspeccionando el código fuente tampoco veo gran cosa, el primer instinto al ver el formulario es probar SQLi, y efectivamente obtenemos un error, lo que nos confirma que es un formulario vulnerable.
### SQLi
Después de probar con un par de payloads consigo romper el formulario y me encuentro con un input de texto que lee: *Consulta del Clima* junto con dicho input y un botón de submit, pruebo con mi ciudad y sale que hay 24ºC, es Agosto en Zaragoza, puedo asegurar que no hay 24ºC, así que voy a echar un ojo al código y a inspeccionar con F12 las solicitudes y peticiones.
Por detrás simplemente se genera una temperatura aleatoria dentro de un rango realista y cualquier nombre que pongamos "funciona", con esto se me ocurre que quizá no esté bien sanitizado y podamos inyectar algo a través del input. Tras probar un par de payloads de otro tipo de inyecciones no tengo éxito y cuando vuelvo a probar SQLi obtengo un error que ningún otro payload me estaba dando, así que utilizado SQLmap para ver si puedo extraer alguna información valiosa.
## Forfeit
[WriteUp de dise0](https://dise0.gitbook.io/h4cker_b00k/ctf/dockerlabs/mirame-dockerlabs-easy)
Después de un rato mirando sin conseguir nada, el cansancio me ha hecho rendirme, mirando en un writeup he visto como un usuario capturaba la petición del login usando burpsuite y hacía el ataque con SQLmap desde ahí, no desde el formulario posterior (como es lógico), de ahí extrae usuarios y contraseñas, además, encuentra un directorio oculto. De ahí saca la contraseña de una imagen usando steghide, y luego con john hace un par de pasos más para terminar de extraer unas credenciales con las que entrar por SSH. Después de eso escala sencillamente consultando los permisos SUID.
## Exploit
## Post