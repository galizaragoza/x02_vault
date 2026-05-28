| **Parámetro**          | **Función**                                     | **Ejemplo de sintaxis**            |
| ---------------------- | ----------------------------------------------- | ---------------------------------- |
| `-d` o `--home=`       | Cambia el directorio home del usuario           | `usermod -d /nuevo/home juan`      |
| `-m` o `--move-home`   | Mueve el contenido del home al nuevo directorio | `usermod -m -d /nuevo/home juan`   |
| `-l` o `--login=`      | Cambia el nombre de login del usuario           | `usermod -l juan_nuevo juan_viejo` |
| `-s` o `--shell=`      | Cambia el shell del usuario                     | `usermod -s /bin/zsh juan`         |
| `-g` o `--gid=`        | Cambia el grupo primario del usuario            | `usermod -g sudo juan`             |
| `-G` o `--groups=`     | Cambia la lista de grupos secundarios           | `usermod -G developers,ftp juan`   |
| `-a` o `--append`      | Agrega grupos sin eliminar los existentes       | `usermod -a -G docker juan`        |
| `-L` o `--lock`        | Bloquea la cuenta del usuario                   | `usermod -L juan`                  |
| `-U` o `--unlock`      | Desbloquea la cuenta del usuario                | `usermod -U juan`                  |
| `-e` o `--expiredate=` | Establece fecha de expiración de la cuenta      | `usermod -e 2024-12-31 juan`       |
| `-u` o `--uid=`        | Cambia el UID del usuario                       | `usermod -u 1500 juan`             |