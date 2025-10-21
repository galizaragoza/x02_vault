# Cheatsheet de escalada de privilegios
## Sanitización de Shell
```
export SHELL=bash
export TERM=xterm
export TERM=xterm-256color
source /etc/skel/.bashrc
```
Si esta da algún error, probar con:
```
script /dev/null -c bash
## Aquí presionamos CTRL+Z
stty raw -echo; fg
>reset
>xterm
export TERM=xterm
export SHELL=bash
``` 
## Reconocimiento inicial
### Información del sistema y kernel
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
### Información de la red
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
### Usuarios y grupos
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
### Procesos en ejecución
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
### Servicios y cron jobs
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
### Archivos con permisos SUID/SGID
```shell
find / -perm -u=s -type f 2>/dev/null
```
Busca archivos con el bit SUID (Set User ID) activado, que se ejecutan con los permisos de su propietario.
```shell
find / -perm -g=s -type f 2>/dev/null
```
Busca archivos con el bit SGID (Set Group ID) activado, que se ejecutan con los permisos de su grupo.
### Archivos editables por el usuario actual
```shell
find / -writable -type d 2>/dev/null
```
Busca directorios en los que el usuario actual tiene permisos de escritura.
```shell
find / -type f -writable 2>/dev/null 2>/dev/null
```
Busca archivos en los que el usuario actual tiene permisos de escritura.
## Vectores comunes
![[linux_file_permission.webp]]
### Binarios SUID/SGID
Los binarios SUID(Set User Identity) y SGID (Set Group Identity) son binarios que se ejecutan con los permisos de sus propietarios. En caso de encontrarnos con un binario con el bit SUID/SGID que sospechamos vulnerable, podemos servirnos de [GTFObins](https://gtfobins.github.io/) para explotar ese vector.
### Sudoers
Un sudoer es un usuario que tiene permisos para ejecutar ciertos comandos en calidad de superusuario (root), según las reglas definidas en /etc/sudoers. A menudo, estos comandos pueden ser abusados y convertirse en vectores de escalada.
### Vulnerabilidades del kernel
El kernel tiene control total sobre el sistema. Vulnerabilidades en su código permiten realizar operaciones no autorizadas, como manipular memoria, eludir controles de permisos, o modificar estructuras críticas para obtener privilegios de root.
### Cronjobs
Cron ejecuta scripts de manera periódica, permanente programada en función de cómo estén configurados, si estos scripts son modificables por usuarios no privilegiados, o si se utilizan rutas relativas sin control, se puede inyectar código que lleve a la escalada.
### Credenciales expuestas
A menudo los equipos tendrán todo tipo de notas y archivos listos para leer que contienen información sensible y pueden llevar a la pivotación o escalada. Para buscar es esencial comprender la estructura de directorios de linux.
![[Directory-Structure.webp]]

#### Configuración del sistema
```
/etc/
/etc/passwd          # Usuarios del sistema
/etc/shadow          # Contraseñas hasheadas
/etc/group           # Grupos del sistema
/etc/sudoers         # Configuración de privilegios sudo
/etc/hosts           # Configuración de DNS local
/etc/crontab         # Tareas programadas del sistema
/etc/cron.d/         # Tareas cron adicionales
/etc/systemd/        # Configuración de servicios systemd
```
#### Directorios de usuarios y Home
```
/home/               # Directorios de usuarios normales
/root/               # Directorio del superusuario
/home/*/.bash_history  # Historial de comandos
/home/*/.ssh/        # Claves SSH de usuarios
/root/.ssh/          # Claves SSH de root
/home/*/.config/     # Configuraciones de aplicaciones
```
#### Directorios de logs y auditoria
```
/var/log/            # Logs del sistema
/var/log/auth.log    # Autenticaciones y sudo
/var/log/syslog      # Logs generales del sistema
/var/log/apache2/    # Logs de Apache
/var/log/nginx/      # Logs de Nginx
/var/log/mysql.log   # Logs de MySQL
```
#### Directorios de servicios y aplicaciones
```
/opt/                # Aplicaciones de terceros
/var/www/            # Directorio web por defecto
/var/www/html/       # Contenido web
/etc/apache2/        # Configuración de Apache
/etc/nginx/          # Configuración de Nginx
/etc/mysql/          # Configuración de MySQL
```
#### Directorios temporales y cache
```
/tmp/                # Archivos temporales (world-writable)
/var/tmp/            # Temporales persistentes
/dev/shm/            # Memoria compartida
```
#### Directorios de backup y configuraciones
```
/backup/             # Backups personalizados
/var/backups/        # Backups del sistema
/etc/backup/         # Backups de configuración
```
## Cheats
### SUID
```php
echo '<?php system("chmod u+s /bin/bash"); ?>' > /dir/ejemplo.php
```
Este código añade el bit SUID al binario bash, después solo es necesario esperar a que se ejecute y lanzar un `bash -p` 
```
`echo "Este es el contenido del archivo." > archivo.txt`
```
Creará un archivo llamado archivo.txt con ese contenido
## !Post-esc