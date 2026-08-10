`journalctl` es la herramienta de línea de comandos para consultar y mostrar los logs del journal de systemd en sistemas Linux. Permite filtrar por unidad, tiempo, prioridad, boot e identificador, formatear la salida en múltiples formatos (incluido JSON) y gestionar el almacenamiento del journal (rotación, vaciado, verificación de integridad). Sustituye en gran medida a las herramientas clásicas de syslog en distribuciones basadas en systemd.

> Sintaxis general: `journalctl [opciones] [MATCHES...]`
> Los `MATCHES` son pares `CAMPO=valor` (p. ej. `_PID=1234`, `_SYSTEMD_UNIT=sshd.service`); varios del mismo campo se combinan con OR, de campos distintos con AND, y `+` separa grupos con OR.

# Fuentes del journal

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `--system` | Muestra mensajes de servicios del sistema y kernel. | `journalctl --system` |
| `--user` | Muestra mensajes de servicios del usuario actual. | `journalctl --user` |
| `-M, --machine=` | Muestra mensajes de un contenedor local en ejecución. | `journalctl --machine=container_name` |
| `-m, --merge` | Intercala entradas de todos los journals disponibles, incluidos remotos. | `journalctl --merge` |
| `-D, --directory=` | Opera sobre un directorio de journal concreto. | `journalctl --directory=/path/to/journal` |
| `-i, --file=` | Opera sobre archivos de journal que coincidan con el glob. | `journalctl --file=*.journal` |
| `--root=` | Opera sobre directorios de journal bajo la raíz indicada. | `journalctl --root=/new/root` |
| `--image=` | Opera sobre el sistema de archivos de una imagen de disco. | `journalctl --image=/path/to/image` |
| `--image-policy=` | Aplica una política de imagen según systemd.image-policy(7). | `journalctl --image-policy=*` |
| `--namespace=` | Muestra datos de log del namespace de journal indicado. | `journalctl --namespace=ns_name` |

# Filtrado por tiempo y cursor

| **Flag**                     | **Descripción**                                                | **Ejemplo**                                |
| ---------------------------- | -------------------------------------------------------------- | ------------------------------------------ |
| `-S, --since=`               | Entradas desde la fecha indicada o posteriores.                | `journalctl --since="2012-10-30 18:17:16"` |
| `-U, --until=`               | Entradas hasta la fecha indicada o anteriores.                 | `journalctl --until="yesterday"`           |
| `-c, --cursor=`              | Entradas desde la posición del cursor indicado.                | `journalctl --cursor=some_cursor`          |
| `--after-cursor=`            | Entradas posteriores a la posición del cursor indicado.        | `journalctl --after-cursor=some_cursor`    |
| `--cursor-file=`             | Usa el cursor del archivo si existe; lo actualiza al terminar. | `journalctl --cursor-file=/path/to/file`   |
| `-b, --boot[=[ID][±offset]]` | Mensajes de un boot concreto (`-b` actual, `-b -1` anterior).  | `journalctl -b -1`                         |
| `--list-boots`               | Lista los boots registrados con sus IDs y rangos de tiempo.    | `journalctl --list-boots`                  |

# Filtrado por unidad e identificador

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-u, --unit=` | Mensajes de la unidad systemd indicada o patrón. | `journalctl --unit=httpd.service` |
| `--user-unit=` | Mensajes de la unidad de sesión de usuario indicada. | `journalctl --user-unit=session-123.scope` |
| `-I, --invocation=` | Mensajes de una invocación concreta de una unidad. | `journalctl -u sshd.service --invocation=-1` |
| `-t, --identifier=` | Mensajes del identificador syslog indicado. | `journalctl --identifier=httpd` |
| `-T, --exclude-identifier=` | Excluye mensajes del identificador syslog indicado. | `journalctl --exclude-identifier=httpd` |

# Filtrado por contenido y prioridad

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-p, --priority=` | Filtra por prioridad o rango (0/emerg .. 7/debug). | `journalctl --priority=err` |
| `--facility=` | Filtra por facility de syslog (lista separada por comas). | `journalctl --facility=user` |
| `-g, --grep=` | Filtra entradas cuyo `MESSAGE=` coincide con la expresión regular. | `journalctl --grep="error pattern"` |
| `--case-sensitive[=BOOLEAN]` | Hace la coincidencia sensible a mayúsculas/minúsculas. | `journalctl --case-sensitive=true` |
| `-k, --dmesg` | Muestra solo mensajes del kernel. | `journalctl --dmesg` |

# Formato de salida

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-o, --output=` | Formato de salida (`short`, `verbose`, `json`, `json-pretty`, `cat`...). | `journalctl --output=json-pretty` |
| `--output-fields=` | Incluye solo los campos indicados (lista separada por comas). | `journalctl -o json --output-fields=PRIORITY,MESSAGE` |
| `--truncate-newline` | Trunca cada mensaje en la primera línea nueva. | `journalctl --truncate-newline` |
| `-n, --lines=` | Muestra las N entradas más recientes (`+N` desde el principio). | `journalctl --lines=10` |
| `-r, --reverse` | Invierte la salida (más nuevas primero). | `journalctl --reverse` |
| `--show-cursor` | Imprime el cursor tras la última entrada. | `journalctl --show-cursor` |
| `--utc` | Expresa las marcas de tiempo en UTC. | `journalctl --utc` |
| `-x, --catalog` | Añade textos explicativos del catálogo de mensajes. | `journalctl --catalog` |
| `--no-hostname` | Oculta el hostname en los mensajes locales. | `journalctl --no-hostname` |
| `--no-full` | Elipsa los campos que no caben en las columnas. | `journalctl --no-full` |
| `-a, --all` | Muestra todos los campos completos, incluso no imprimibles. | `journalctl --all` |
| `-q, --quiet` | Suprime los mensajes informativos. | `journalctl --quiet` |
| `--no-pager` | No envía la salida a un pager. | `journalctl --no-pager` |
| `-e, --pager-end` | Salta al final de la salida en el pager. | `journalctl --pager-end` |

# Modo seguimiento (follow)

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-f, --follow` | Muestra las entradas recientes y sigue las nuevas en tiempo real. | `journalctl -u nginx -f` |
| `--no-tail` | En modo follow muestra todas las líneas almacenadas, no solo el final. | `journalctl --follow --no-tail` |
| `--synchronize-on-exit=` | Emite una solicitud de sincronización al salir de `--follow` (booleano). | `journalctl -f --synchronize-on-exit=true` |

# Acciones de mantenimiento y almacenamiento

Estas opciones no muestran logs: ejecutan una acción sobre el journal y terminan.

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `--disk-usage` | Muestra el espacio en disco ocupado por los archivos de journal. | `journalctl --disk-usage` |
| `--vacuum-size=` | Reduce el journal hasta el tamaño máximo indicado. | `journalctl --vacuum-size=500M` |
| `--vacuum-time=` | Elimina entradas más antiguas que el tiempo indicado. | `journalctl --vacuum-time=2weeks` |
| `--vacuum-files=` | Conserva como máximo el nº de archivos de journal indicado. | `journalctl --vacuum-files=10` |
| `--rotate` | Fuerza la rotación de los archivos de journal. | `journalctl --rotate` |
| `--sync` | Vuelca a disco los datos en memoria y espera a que se completen. | `journalctl --sync` |
| `--flush` | Vuelca los logs de `/run` a `/var/log/journal` (almacenamiento persistente). | `journalctl --flush` |
| `--relinquish-var` | Deja de escribir en `/var/log/journal` (vuelve a `/run`). | `journalctl --relinquish-var` |
| `--smart-relinquish-var` | Como `--relinquish-var` solo si `/var` está en un sistema de archivos propio. | `journalctl --smart-relinquish-var` |
| `--verify` | Verifica la integridad/consistencia interna de los archivos de journal. | `journalctl --verify` |
| `--header` | Muestra la información de cabecera de los archivos de journal. | `journalctl --header` |

# Catálogo de mensajes y campos

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `--list-catalog` | Lista las entradas del catálogo de mensajes. | `journalctl --list-catalog` |
| `--dump-catalog` | Vuelca el contenido completo del catálogo de mensajes. | `journalctl --dump-catalog` |
| `--update-catalog` | Reconstruye el índice binario del catálogo de mensajes. | `journalctl --update-catalog` |
| `-F, --field=` | Lista todos los valores presentes para un campo concreto. | `journalctl --field=_SYSTEMD_UNIT` |
| `-N, --fields` | Lista todos los nombres de campo presentes en el journal. | `journalctl --fields` |

# Integridad FSS (Forward Secure Sealing)

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `--setup-keys` | Genera un par de claves FSS para el sellado del journal. | `journalctl --setup-keys` |
| `--interval=` | Intervalo de cambio de la clave de sellado FSS. | `journalctl --setup-keys --interval=10min` |
| `--verify-key=` | Clave de verificación FSS usada con `--verify`. | `journalctl --verify --verify-key=key` |
| `--force` | Fuerza la recreación de claves FSS con `--setup-keys`. | `journalctl --setup-keys --force` |

# Ayuda y versión

| **Flag** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-h, --help` | Muestra la ayuda de uso. | `journalctl --help` |
| `--version` | Muestra la versión de systemd/journalctl. | `journalctl --version` |

## Notas

- Filtrar por campos directos es más rápido que `--grep`: `journalctl _UID=1000` o `journalctl _PID=1234`.
- El almacenamiento persistente requiere que exista `/var/log/journal`; `--flush` lo puebla tras crearlo.
- Relacionadas: [[systemctl]], [[dmesg]], [[logrotate]], [[syslog-ng]].
