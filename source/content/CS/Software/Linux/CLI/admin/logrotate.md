| **Parámetro**      | **Función**                                   | **Ejemplo de sintaxis**                       |
| ------------------ | --------------------------------------------- | --------------------------------------------- |
| `-f` o `--force`   | Fuerza la rotación incluso si no es necesario | `logrotate -f /etc/logrotate.conf`            |
| `-d` o `--debug`   | Modo debug, no realiza cambios reales         | `logrotate -d /etc/logrotate.d/nginx`         |
| `-v` o `--verbose` | Modo verboso                                  | `logrotate -v /etc/logrotate.conf`            |
| `-s` o `--state=`  | Especifica archivo de estado alternativo      | `logrotate -s /tmp/state /etc/logrotate.conf` |

# .conf
```
daily                     # Rotación diaria
weekly                    # Rotación semanal
monthly                   # Rotación mensual
rotate 30                 # Mantener 30 archivos rotados
compress                  # Comprimir archivos rotados
delaycompress             # Comprimir en la siguiente rotación
missingok                 # No generar error si falta el log
notifempty                # No rotar si el archivo está vacío
create 640 user group     # Permisos y dueño del nuevo archivo
postrotate/endscript      # Comandos a ejecutar después de rotar
```