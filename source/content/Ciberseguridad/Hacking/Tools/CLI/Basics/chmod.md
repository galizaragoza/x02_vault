
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
