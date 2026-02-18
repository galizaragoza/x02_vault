| **Parámetro**              | **Función**                                           | **Ejemplo de sintaxis**                |
| ---------------------- | ------------------------------------------------- | ---------------------------------- |
| `-m` o `--create-home` | Crea el directorio home del usuario               | `useradd -m juan`                  |
| `-d` o `--home-dir=`   | Especifica el directorio home personalizado       | `useradd -d /home/custom juan`     |
| `-s` o `--shell=`      | Define el shell por defecto del usuario           | `useradd -s /bin/bash juan`        |
| `-g` o `--gid=`        | Especifica el GID o nombre del grupo primario     | `useradd -g developers juan`       |
| `-G` o `--groups=`     | Lista de grupos secundarios (separados por comas) | `useradd -G sudo,www-data juan`    |
| `-u` o `--uid=`        | Define un UID específico para el usuario          | `useradd -u 1500 juan`             |
| `-e` o `--expiredate=` | Fecha de expiración de la cuenta (YYYY-MM-DD)     | `useradd -e 2024-12-31 juan`       |
| `-p` o `--password=`   | Establece la contraseña (encriptada)              | `useradd -p '$6$saltrandom' juan`  |
| `-k` o `--skel=`       | Directorio skeleton para copiar al home           | `useradd -k /etc/skel_custom juan` |
| `-r` o `--system`      | Crea una cuenta de sistema (sin home por defecto) | `useradd -r systemuser`            |
| `-D` o `--defaults`    | Muestra o cambia los valores por defecto          | `useradd -D -b /home`              |