# ¿Qué es un File System?

![[linux_file_permission.webp|393x267]]

Un filesystem o sistema de archivos consiste en una serie de normas y procesos mediante los cuáles se rige la gestión del almacenamiento en un dispositivo. Esto es vital para la organización, lectura, escritura y orden de los archivos contenidos en una unidad de almacenamiento, toma el rol de 'middleman' entre el OS y el almacenamiento conectado y según la versión, ayuda a mantener controles de acceso, seguridad, atributos y convenciones de nombres.
Básicamente, es un conjunto de instrucciones necesarias para poder acceder y modificar archivos, el filesystem sabe donde están, quién puede acceder a ellos, como se llaman...

# Los FS de Linux
- ext2
- ext3
- NFS
- CDFS
- Swap
- MBR

# Estructura general Linux
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

# /proc
`/proc` es un filesystem virtual que almacena información sobre procesos, file handles, kernel options...

## Fuentes / recursos
[Keepcoding blog](https://keepcoding.io/blog/que-es-un-sistema-de-archivos/)
[FAT32 vs exFAT vs NTFS - Windows File Systems](https://youtu.be/bYjQakUxeVY)
[Linux File Structure](https://youtu.be/bbmWOjuFmgA?list=PL65_wYSEg5HdGgpkBRymedI24hCXWqB6l)