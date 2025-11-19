# Comandos básicos
### grep

| Parámetro        | Función                                    | Ejemplo de sintaxis                               |
| ---------------- | ------------------------------------------ | ------------------------------------------------- |
| `-i`             | Ignorar mayúsculas/minúsculas              | `grep -i "password" config.php`                   |
| `-r` / `-R`      | Recursivo en directorios                   | `grep -r "DB_PASSWORD" /var/www`                  |
| `-E`             | Regex extendida (sin escapar)              | `grep -E "[0-9]{4}-[0-9]{4}-[0-9]{4}-[0-9]{4}" *` |
| `-P`             | Regex Perl (lookarounds, \d, etc.)         | `grep -P '(?<=api_key=)[A-Za-z0-9]{32}' files`    |
| `-w`             | Palabra completa                           | `grep -w "admin" users.txt`                       |
| `-n`             | Mostrar número de línea                    | `grep -n "root:" /etc/passwd`                     |
| `-c`             | Contar coincidencias                       | `grep -c "error" logs/*.log`                      |
| `-l`             | Solo nombres de archivos con match         | `grep -l "ssh-rsa" ~/.ssh/*`                      |
| `-v`             | Invertir (líneas que NO coinciden)         | `grep -v "^#" config.conf`                        |
| `-A N`           | Mostrar N líneas después                   | `grep -A 3 "password" app.js`                     |
| `-B N`           | Mostrar N líneas antes                     | `grep -B 2 "error" server.log`                    |
| `-C N`           | Mostrar N líneas antes y después           | `grep -C 5 "login failed" auth.log`               |
| `--color=always` | Forzar color (útil con pipes)              | `grep --color=always "ERROR" logs                 |
| `-a`             | Tratar binarios como texto                 | `grep -a "password" binary.exe`                   |
| `-z`             | Archivos terminados en NUL (para xargs -0) | `find . -type f -print0                           |
### head

| Parámetro | Función                       | Ejemplo de sintaxis    |
| --------- | ----------------------------- | ---------------------- |
| `-n N`    | Mostrar N primeras líneas     | `head -n 10 file.txt`  |
| `-c N`    | Mostrar N primeros bytes      | `head -c 512 file.bin` |
| `-q` `    | No mostrar nombres de archivo | `head -q *.log`        |
### strings
|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-n N`|Longitud mínima de string|`strings -n 10 binary`|
|`-t d/o/x`|Mostrar offset (dec/oct/hex)|`strings -t x executable`|
|`-a`|Escanear todo el archivo|`strings -a file.exe`|

### find
| Parámetro         | Función                 | Ejemplo de sintaxis                   |
| ----------------- | ----------------------- | ------------------------------------- |
| `-name "pat"`     | Buscar por nombre       | `find / -name "*.conf"`               |
| `-type f/d/l`     | Tipo archivo/dir/enlace | `find . -type f`                      |
| `-perm mode`      | Permisos exactos        | `find . -perm 777`                    |
| `-user name`      | Propietario             | `find /home -user alice`              |
| `-exec cmd {} \;` | Ejecutar comando        | `find . -name "*.tmp" -exec rm {} \;` |

### mv
|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-i`|Preguntar antes sobrescribir|`mv -i old new`|
|`-v`|Verbose|`mv -v file1 dir/`|
|`-f`|Forzar sin preguntar|`mv -f source dest`|

### cp
|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-r/-R`|Copia recursiva|`cp -r dir1/ dir2/`|
|`-p`|Conservar permisos y fechas|`cp -p file backup/`|
|`-v`|Verbose|`cp -v *.txt backup/`|
### chown y chgrp
|Binario|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|---|
|chown|`-R`|Recursivo|`chown -R user:group /var/www`|
|chown|`user`|Cambiar solo usuario|`chown alice file.txt`|
|chown|`user:group`|Cambiar usuario y grupo|`chown www-data:www-data /var/www`|
|chown|`:group`|Cambiar solo grupo (como chgrp)|`chown :admins secret.txt`|
|chgrp|`-R`|Recursivo|`chgrp -R devs project/`|
|chgrp|`group`|Cambiar grupo|`chgrp admins config.conf`|
### chmod
| Binario | Parámetro / Modo | Significado detallado                                    | Ejemplo de sintaxis           |
| ------- | ---------------- | -------------------------------------------------------- | ----------------------------- |
| chmod   | `-R`             | Aplicar recursivamente                                   | `chmod -R 755 /var/www`       |
| chmod   | `777`            | rwx para owner, group y others (todos pueden todo)       | `chmod 777 shell.php`         |
| chmod   | `755`            | owner: rwx │ group & others: r-x (ejecutable/directorio) | `chmod 755 exploit`           |
| chmod   | `700`            | Solo owner: rwx │ nadie más nada                         | `chmod 700 ~/.ssh`            |
| chmod   | `644`            | owner: rw- │ group & others: r-- (archivos legibles)     | `chmod 644 config.php`        |
| chmod   | `600`            | Solo owner: rw- │ nadie más nada                         | `chmod 600 id_rsa`            |
| chmod   | `666`            | Todos pueden leer y escribir (sin ejecución)             | `chmod 666 /tmp/loot.txt`     |
| chmod   | `444`            | Solo lectura para todos                                  | `chmod 444 /etc/passwd`       |
| chmod   | `400`            | Solo lectura para owner, nada para resto                 | `chmod 400 private.key`       |
| chmod   | `6777`           | Como 777 + SetUID + SetGID (peligroso)                   | `chmod 6777 /usr/bin/special` |
| chmod   | `4777`           | SetUID + 777 (ejecuta como owner)                        | `chmod 4777 binary`           |
| chmod   | `2777`           | SetGID + 777 (hereda grupo en directorios)               | `chmod 2777 /shared`          |
| chmod   | `u+x`            | Añadir ejecución al owner                                | `chmod u+x script.sh`         |
| chmod   | `o-w`            | Quitar escritura a others                                | `chmod o-w sensitive.conf`    |
| chmod   | `+s`             | SetUID/SetGID (según contexto)                           | `chmod +s /bin/tool`          |
### echo
| Parámetro | Función                       | Ejemplo de sintaxis      |
| --------- | ----------------------------- | ------------------------ |
| `-n`      | Sin salto de línea            | `echo -n "text"`         |
| `-e`      | Habilitar secuencias \n\t\etc | `echo -e "line1\nline2"` |

# Comandos específicos distro
### APT
 ![[apt-cs.png|360x450]]
