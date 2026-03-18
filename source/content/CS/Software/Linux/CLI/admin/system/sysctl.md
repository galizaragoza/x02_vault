`sysctl` es una herramienta para examinar y modificar parámetros del kernel en tiempo de ejecución. Accede a variables del kernel a través del sistema de archivos `/proc/sys/`.

| **Parámetro**         | **Función**                                               | **Ejemplo de sintaxis**                  |
| ----------------- | ----------------------------------------------------- | ------------------------------------ |
| `-a` o `--all`    | Muestra todas las variables disponibles               | `sysctl -a`                          |
| `-n` o `--values` | Muestra solo valores (sin nombres)                    | `sysctl -n kernel.hostname`          |
| `-e` o `--ignore` | Ignora errores de variables desconocidas              | `sysctl -e variable.inexistente`     |
| `-w` o `--write`  | Cambia variable temporalmente                         | `sysctl -w kernel.panic=60`          |
| `-p` o `--load`   | Carga configuración desde archivo                     | `sysctl -p /etc/sysctl.conf`         |
| `--system`        | Carga configuración de todos los archivos del sistema | `sysctl --system`                    |
| `-q` o `--quiet`  | Modo silencioso (sin output)                          | `sysctl -q -w net.ipv4.ip_forward=1` |
| `-b` o `--binary` | Muestra valores sin nueva línea                       | `sysctl -b kernel.ostype`            |
| `-A`              | Muestra todas las variables en formato tabla          | `sysctl -A`                          |
| `--deprecated`    | Incluye parámetros obsoletos                          | `sysctl -a --deprecated`             |
| `--pattern=`      | Filtra variables por patrón                           | `sysctl -a --pattern="net.ipv4"`     |
