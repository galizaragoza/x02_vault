
|Binario|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|---|
|chown|`-R`|Recursivo|`chown -R user:group /var/www`|
|chown|`user`|Cambiar solo usuario|`chown alice file.txt`|
|chown|`user:group`|Cambiar usuario y grupo|`chown www-data:www-data /var/www`|
|chown|`:group`|Cambiar solo grupo (como chgrp)|`chown :admins secret.txt`|
|chgrp|`-R`|Recursivo|`chgrp -R devs project/`|
|chgrp|`group`|Cambiar grupo|`chgrp admins config.conf`|
