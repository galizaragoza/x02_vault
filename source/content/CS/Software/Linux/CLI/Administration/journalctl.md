journalctl es una herramienta de línea de comandos para consultar y mostrar logs del journal de systemd en sistemas Linux. Permite filtrar, formatear y gestionar entradas de logs de servicios, kernel y usuarios.

| **Flag**                     | **Descripción**                                                                         | **Ejemplo**                                   |
| ---------------------------- | --------------------------------------------------------------------------------------- | --------------------------------------------- |
| `--system`                   | Muestra mensajes de servicios del sistema y kernel.                                     | `journalctl --system`                         |
| `--user`                     | Muestra mensajes de servicios del usuario actual.                                       | `journalctl --user`                           |
| `-M, --machine=`             | Muestra mensajes de un contenedor local en ejecución. Especifica nombre de contenedor.  | `journalctl --machine=container_name`         |
| `-m, --merge`                | Muestra entradas intercaladas de todos los journals disponibles, incluyendo remotos.    | `journalctl --merge`                          |
| `-D, --directory=`           | Opera en un directorio de journal especificado en lugar de los predeterminados.         | `journalctl --directory=/path/to/journal`     |
| `-i, --file=`                | Opera en archivos de journal que coincidan con el glob especificado.                    | `journalctl --file=*.journal`                 |
| `--root=`                    | Opera en directorios de journal bajo el directorio raíz especificado.                   | `journalctl --root=/new/root`                 |
| `--image=`                   | Opera en el sistema de archivos de una imagen de disco o nodo de dispositivo de bloque. | `journalctl --image=/path/to/image`           |
| `--image-policy=`            | Toma una cadena de política de imagen según systemd.image-policy(7).                    | `journalctl --image-policy=*`                 |
| `--namespace=`               | Muestra datos de log del namespace de journal especificado.                             | `journalctl --namespace=ns_name`              |
| `-S, --since=`               | Muestra entradas desde la fecha especificada o posterior.                               | `journalctl --since="2012-10-30 18:17:16"`    |
| `-U, --until=`               | Muestra entradas hasta la fecha especificada o anterior.                                | `journalctl --until="yesterday"`              |
| `-c, --cursor=`              | Muestra entradas desde la ubicación del cursor especificado.                            | `journalctl --cursor=some_cursor`             |
| `--after-cursor=`            | Muestra entradas después de la ubicación del cursor especificado.                       | `journalctl --after-cursor=some_cursor`       |
| `--cursor-file=`             | Usa cursor de archivo si existe; actualiza al final.                                    | `journalctl --cursor-file=/path/to/file`      |
| `-b, --boot[=[ID][±offset]`  | Muestra mensajes de un boot específico.                                                 |                                               |
| `-u, --unit=`                | Muestra mensajes de la unidad systemd especificada o patrón.                            | `journalctl --unit=httpd.service`             |
| `--user-unit=`               | Muestra mensajes de la unidad de sesión de usuario especificada.                        | `journalctl --user-unit=session-123.scope`    |
| `-I, --invocation=`          | Muestra mensajes de una invocación específica de unidad.                                | `journalctl --invocation=ID`                  |
| `-t, --identifier=`          | Muestra mensajes del identificador syslog especificado.                                 | `journalctl --identifier=httpd`               |
| `-T, --exclude-identifier=`  | Excluye mensajes del identificador syslog especificado.                                 | `journalctl --exclude-identifier=httpd`       |
| `-p, --priority=`            | Filtra por prioridades o rangos de mensajes.                                            | `journalctl --priority=error`                 |
| `--facility=`                | Filtra por facility syslog (lista separada por comas).                                  | `journalctl --facility=user`                  |
| `-g, --grep=`                | Filtra entradas donde MESSAGE= coincide con expresión regular.                          | `journalctl --grep="error pattern"`           |
| `--case-sensitive[=BOOLEAN]` | Hace coincidir patrones sensible a mayúsculas/minúsculas.                               | `journalctl --case-sensitive=true`            |
| `-k, --dmesg`                | Muestra solo mensajes del kernel.                                                       | `journalctl --dmesg`                          |
| `-o, --output=`              | Controla formato de salida (ej: short, verbose, json).                                  | `journalctl --output=json`                    |
| `--truncate-newline`         | Trunca mensajes en la primera línea nueva.                                              | `journalctl --truncate-newline`               |
| `--output-fields=`           | Incluye solo campos especificados en salida (lista separada por comas).                 | `journalctl --output-fields=PRIORITY,MESSAGE` |
| `-n, --lines=`               | Muestra número reciente de eventos.                                                     | `journalctl --lines=10`                       |
| `-r, --reverse`              | Invierte salida (nuevos primero).                                                       | `journalctl --reverse`                        |
| `--show-cursor`              | Muestra cursor después de la última entrada.                                            | `journalctl --show-cursor`                    |
| `--utc`                      | Expresa tiempo en UTC.                                                                  | `journalctl --utc`                            |
| `-x, --catalog`              | Agrega textos explicativos de catálogo de mensajes.                                     | `journalctl --catalog`                        |
| `--no-hostname`              | No muestra hostname para mensajes locales.                                              | `journalctl --no-hostname`                    |
| `--no-full`                  | Elipsa campos que no caben en columnas.                                                 | `journalctl --no-full`                        |
| `-a, --all`                  | Muestra todos los campos completos, incluso no imprimibles.                             | `journalctl --all`                            |
| `-f, --follow`               | Muestra entradas recientes y sigue nuevas.                                              | `journalctl --follow`                         |
| `--no-tail`                  | Muestra todas las líneas almacenadas en modo follow.                                    | `journalctl --no-tail`                        |
| `-q, --quiet`                | Suprime mensajes informativos.                                                          | `journalctl --quiet`                          |
| `--synchronize-on-exit=`     | Emite solicitud de sincronización en --follow al salir (booleano).                      | `journalctl --synchronize-on-exit=true`       |
| `--no-pager`                 | No envía salida a pager.                                                                | `journalctl --no-pager`                       |
| `-e, --pager-end`            | Salta al final en pager.                                                                | `journalctl --pager-end`                      |
| `--interval=`                | Especifica intervalo para clave de sellado FSS.                                         | `journalctl --interval=10min`                 |
| `--verify-key=`              | Especifica clave de verificación FSS para --verify.                                     | `journalctl --verify-key=key`                 |
| `--force`                    | Fuerza recreación de claves FSS en --setup-keys.                                        | `journalctl --setup-keys --force`             |
