SSHFS es un sistema de archivos basado en FUSE que permite montar un sistema de archivos remoto a través de una conexión SSH segura. Permite interactuar con archivos remotos como si estuvieran almacenados localmente.

|Parámetro/Opción|Función|Ejemplo de sintaxis|
|---|---|---|
|`-p`|Especifica el puerto SSH|`sshfs -p 2222 usuario@host:/ruta/remota /ruta/local`|
|`-C`|Habilita compresión|`sshfs -C usuario@host:/ruta/remota /ruta/local`|
|`-o allow_other`|Permite a otros usuarios acceder al montaje|`sshfs -o allow_other usuario@host:/ruta/remota /ruta/local`|
|`-o IdentityFile`|Especifica archivo de identidad (clave privada)|`sshfs -o IdentityFile=~/.ssh/id_rsa usuario@host:/ruta/remota /ruta/local`|
|`-o reconnect`|Reconecta automáticamente en caso de desconexión|`sshfs -o reconnect usuario@host:/ruta/remota /ruta/local`|
|`-o idmap=user`|Mapea IDs de usuario|`sshfs -o idmap=user usuario@host:/ruta/remota /ruta/local`|
|`-o cache_timeout=N`|Establece tiempo de caché en segundos|`sshfs -o cache_timeout=3600 usuario@host:/ruta/remota /ruta/local`|
|`-o compression=no`|Desactiva compresión|`sshfs -o compression=no usuario@host:/ruta/remota /ruta/local`|
|`-o kernel_cache`|Usa caché del kernel para mejor rendimiento|`sshfs -o kernel_cache usuario@host:/ruta/remota /ruta/local`|