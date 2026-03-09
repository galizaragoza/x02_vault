# ¿Qué es un File System?


![[linux_file_permission.webp|412x321]]![[sd-figure.png|380x322]]

Un filesystem o sistema de archivos consiste en una serie de normas y procesos mediante los cuáles se rige la gestión del almacenamiento en un dispositivo. Esto es vital para la organización, lectura, escritura y orden de los archivos contenidos en una unidad de almacenamiento, toma el rol de 'middleman' entre el OS y el almacenamiento conectado y según la versión, ayuda a mantener controles de acceso, seguridad, atributos y convenciones de nombres.
Básicamente, es un conjunto de instrucciones necesarias para poder acceder y modificar archivos, el filesystem sabe donde están, quién puede acceder a ellos, como se llaman...

# Los FS de Linux
- ext2
- ext3
- NFS
- CDFS
- Swap
- MBR




# Linux dir tree
![[linux-dir-tree.png|500x250]]

## /bin
Ejecutables, normalmente en binario pero algunos scripts.


## /dev



- [Major and Minor numbers in the Linux kernel](https://medium.com/@linuxrootroom/major-and-minor-numbers-in-linux-kernel-0c54af7a0ab8)

## /etc
Directorio de  configuración principal del sistema

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


## /home 
Directorios personales de los usuarios del sistema, `/root` es parecido a es como la home del superusuario.
 
```
/home/               # Directorios de usuarios normales
/root/               # Directorio del superusuario
/home/*/.bash_history  # Historial de comandos
/home/*/.ssh/        # Claves SSH de usuarios
/root/.ssh/          # Claves SSH de root
/home/*/.config/     # Configuraciones de aplicaciones
```


## /boot
## /media
## /opt


## /lib
De 'library', contiene código que utilizan los ejecutables. Hay librerías estáticas y compartidas.
- /lib --> Debería contener solo librerías compartidas
- /usr/lib --> Contiene ambas variedades y otros archivos auxiliares


## /proc
`/proc` es un filesystem virtual que almacena información sobre procesos, file handles, kernel options...


## /run
Contiene información sobre PIDs, sockets, status records y a menudo logs del sistema, puede estar también en /var/run


## /sys


## /tmp
Directorio temporal
```
/tmp/                # Archivos temporales (world-writable)
/var/tmp/            # Temporales persistentes
/dev/shm/            # Memoria compartida
```


## /usr
### /include
### /local
### /man
### /share


## /var
Directorio donde se almacena información variable (caches, logs, tracking, mail...) Entre otras cosas (por ejemplo /var/www/html que aloja páginas web.)
```
/var/log/            # Logs del sistema
/var/log/auth.log    # Autenticaciones y sudo
/var/log/syslog      # Logs generales del sistema
/var/log/apache2/    # Logs de Apache
/var/log/nginx/      # Logs de Nginx
/var/log/mysql.log   # Logs de MySQL
```


## /opt
Software opcional de terceros


## Fuentes / recursos
[Keepcoding blog](https://keepcoding.io/blog/que-es-un-sistema-de-archivos/)
[FAT32 vs exFAT vs NTFS - Windows File Systems](https://youtu.be/bYjQakUxeVY)
[Linux File Structure](https://youtu.be/bbmWOjuFmgA?list=PL65_wYSEg5HdGgpkBRymedI24hCXWqB6l)