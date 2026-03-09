Gestionar eventos de dispositivos y consultar información de hardware.
```
udevadm [comando] [opciones]
```


# Params
## info
| Parámetro (Flag)                                   | Función                                         | Ejemplo de Sintaxis                                          |
| -------------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| `-q, --query=TYPE`                                 | Consulta tipo específico (name, symlink, etc.). | `udevadm info -q name /dev/sda`                              |
| `--property=NAME`                                  | Limita propiedades (con --query=property).      | `udevadm info --query=property --property=ID_MODEL /dev/sda` |
| `--value`                                          | Imprime solo valores de propiedades.            | `udevadm info --query=property --value /dev/sda`             |
| `-p, --path=DEVPATH`                               | Especifica ruta /sys/.                          | `udevadm info -p /sys/class/block/sda`                       |
| `-n, --name=FILE`                                  | Especifica nodo o symlink.                      | `udevadm info -n /dev/sda`                                   |
| `-r, --root`                                       | Imprime rutas absolutas.                        | `udevadm info -q name -r /dev/sda`                           |
| `-a, --attribute-walk`                             | Imprime propiedades sysfs para reglas.          | `udevadm info -a /dev/sda`                                   |
| `-t, --tree`                                       | Muestra estructura de árbol sysfs.              | `udevadm info -t /sys/class/block`                           |
| `-x, --export`                                     | Imprime como key/value.                         | `udevadm info --query=property -x /dev/sda`                  |
| `-P, --export-prefix=NAME`                         | Agrega prefijo a keys.                          | `udevadm info --query=property -x -P DEV_ /dev/sda`          |
| `-d, --device-id-of-file=FILE`                     | Imprime major/minor de archivo.                 | `udevadm info -d /dev/sda`                                   |
| `-e, --export-db`                                  | Exporta base de datos udev.                     | `udevadm info -e`                                            |
| `-c, --cleanup-db`                                 | Limpia base de datos.                           | `udevadm info -c`                                            |
| `-w[SECONDS], --wait-for-initialization[=SECONDS]` | Espera inicialización.                          | `udevadm info -w 10 /dev/sda`                                |
| `--subsystem-match[=SUBSYSTEM]`                    | Filtra por subsystem (con --export-db).         | `udevadm info -e --subsystem-match=block`                    |
| `--subsystem-nomatch[=SUBSYSTEM]`                  | Excluye subsystem (con --export-db).            | `udevadm info -e --subsystem-nomatch=net`                    |
| `--attr-match[=FILE[=VALUE]]`                      | Filtra por atributo (con --export-db).          | `udevadm info -e --attr-match=model=SSD`                     |
| `--attr-nomatch[=FILE[=VALUE]]`                    | Excluye atributo (con --export-db).             | `udevadm info -e --attr-nomatch=vendor=Unknown`              |
| `--property-match[=KEY=VALUE]`                     | Filtra por propiedad (con --export-db).         | `udevadm info -e --property-match=ID_BUS=usb`                |
| `--tag-match[=TAG]`                                | Filtra por tag (con --export-db).               | `udevadm info -e --tag-match=seat`                           |
| `--sysname-match[=NAME]`                           | Filtra por nombre /sys/ (con --export-db).      | `udevadm info -e --sysname-match=sda`                        |
| `--name-match[=NAME]`                              | Filtra por nombre /dev/ (con --export-db).      | `udevadm info -e --name-match=sda`                           |
| `--parent-match[=NAME]`                            | Filtra por parent (con --export-db).            | `udevadm info -e --parent-match=pci0000:00`                  |
| `--initialized-match`                              | Filtra inicializados (con --export-db).         | `udevadm info -e --initialized-match`                        |
| `--initialized-nomatch`                            | Excluye inicializados (con --export-db).        | `udevadm info -e --initialized-nomatch`                      |
| `--json=MODE`                                      | Formato JSON (short, pretty, off).              | `udevadm info --json=pretty /dev/sda`                        |
| `--no-pager`                                       | Deshabilita pager.                              | `udevadm info --no-pager /dev/sda`                           |

## monitor
|Parámetro (Flag)|Función|Ejemplo de Sintaxis|
|---|---|---|
|`-k, --kernel`|Imprime eventos kernel.|`udevadm monitor -k`|
|`-u, --udev`|Imprime eventos udev.|`udevadm monitor -u`|
|`-p, --property`|Imprime propiedades de eventos.|`udevadm monitor -p`|
|`-s, --subsystem-match=string[/string]`|Filtra por subsystem/devtype.|`udevadm monitor -s block`|
|`-t, --tag-match=string`|Filtra por tag udev.|`udevadm monitor -t seat`|

## trigger
|Parámetro (Flag)|Función|Ejemplo de Sintaxis|
|---|---|---|
|`-v, --verbose`|Imprime dispositivos activados.|`udevadm trigger -v`|
|`-n, --dry-run`|Simula sin activar.|`udevadm trigger -n`|
|`-q, --quiet`|Suprime logs de error.|`udevadm trigger -q`|
|`-t, --type=TYPE`|Tipo de trigger (all, devices, subsystems).|`udevadm trigger -t devices`|
|`-c, --action=ACTION`|Acción de evento.|`udevadm trigger -c add`|
|`--prioritized-subsystem=SUBSYSTEM[,SUBSYSTEM...]`|Prioriza subsystems.|`udevadm trigger --prioritized-subsystem=block,net`|
|`-s, --subsystem-match=SUBSYSTEM`|Filtra por subsystem.|`udevadm trigger -s block`|
|`-S, --subsystem-nomatch=SUBSYSTEM`|Excluye subsystem.|`udevadm trigger -S net`|
|`-a, --attr-match=ATTRIBUTE=VALUE`|Filtra por atributo sysfs.|`udevadm trigger -a model=SSD`|
|`-A, --attr-nomatch=ATTRIBUTE=VALUE`|Excluye atributo sysfs.|`udevadm trigger -A vendor=Unknown`|
|`-p, --property-match=PROPERTY=VALUE`|Filtra por propiedad.|`udevadm trigger -p ID_BUS=usb`|
|`-g, --tag-match=TAG`|Filtra por tag.|`udevadm trigger -g seat`|
|`-y, --sysname-match=NAME`|Filtra por nombre /sys/.|`udevadm trigger -y sda`|
|`--name-match=NAME`|Filtra por ruta dispositivo.|`udevadm trigger --name-match=/dev/sda`|
|`-b, --parent-match=SYSPATH`|Activa hijos de dispositivo.|`udevadm trigger -b /sys/class/block`|
|`--initialized-match`|Activa inicializados.|`udevadm trigger --initialized-match`|
|`--initialized-nomatch`|Activa no inicializados.|`udevadm trigger --initialized-nomatch`|
|`--include-parents`|Incluye parents.|`udevadm trigger --include-parents`|
|`-w, --settle`|Espera fin de eventos.|`udevadm trigger -w`|
|`--uuid`|Asigna UUID a eventos.|`udevadm trigger --uuid`|
|`--wait-daemon[=SECONDS]`|Espera daemon udev.|`udevadm trigger --wait-daemon=10`|

## test
| Parámetro (Flag)                | Función                   | Ejemplo de Sintaxis                          |
| ------------------------------- | ------------------------- | -------------------------------------------- |
| `-a, --action=ACTION`           | Acción simulada.          | `udevadm test -a add /dev/sda`               |
| ``-N, --resolve-names=early     | late                      | never``                                      |
| `-D DIR, --extra-rules-dir=DIR` | Carga reglas adicionales. | `udevadm test -D /etc/udev/rules.d /dev/sda` |
| `-v, --verbose`                 | Logs verbose.             | `udevadm test -v /dev/sda`                   |
| `--json=MODE`                   | Formato JSON.             | `udevadm test --json=pretty /dev/sda`        |

## settle
|Parámetro (Flag)|Función|Ejemplo de Sintaxis|
|---|---|---|
|`-t, --timeout=SECONDS`|Tiempo de espera.|`udevadm settle -t 60`|
|`-E, --exit-if-exists=FILE`|Sale si existe archivo.|`udevadm settle -E /dev/sda`|

## control
| Parámetro (Flag)           | Función                       | Ejemplo de Sintaxis                  |
| -------------------------- | ----------------------------- | ------------------------------------ |
| `-e, --exit`               | Señala salida a daemon.       | `udevadm control -e`                 |
| `-l, --log-level=value`    | Nivel de log.                 | `udevadm control -l debug`           |
| `-s, --stop-exec-queue`    | Detiene ejecución de eventos. | `udevadm control -s`                 |
| `-S, --start-exec-queue`   | Inicia ejecución.             | `udevadm control -S`                 |
| `-R, --reload`             | Recarga reglas y DB.          | `udevadm control -R`                 |
| `-p, --property=KEY=value` | Propiedad global.             | `udevadm control -p TEST=1`          |
| `-m, --children-max=value` | Máx eventos concurrentes.     | `udevadm control -m 128`             |
| `--ping`                   | Verifica daemon corriendo.    | `udevadm control --ping`             |
| `--trace=BOOL`             | Habilita trace.               | `udevadm control --trace=true`       |
| `--revert`                 | Revierte opciones previas.    | `udevadm control --revert`           |
| `-t, --timeout=seconds`    | Tiempo de espera.             | `udevadm control -t 30`              |
| `--load-credentials`       | Carga credenciales.           | `udevadm control --load-credentials` |

## wait
|Parámetro (Flag)|Función|Ejemplo de Sintaxis|
|---|---|---|
|`-t, --timeout=SECONDS`|Tiempo de espera.|`udevadm wait -t 30 /dev/sda`|
|`--initialized=BOOL`|Verifica inicialización.|`udevadm wait --initialized=true /dev/sda`|
|`--removed`|Espera remoción.|`udevadm wait --removed /dev/sda`|
|`--settle`|Vigila cola de eventos.|`udevadm wait --settle /dev/sda`|


# Recursos
### [udevadm(8)](https://www.man7.org/linux/man-pages/man8/udevadm.8.html)
### [systemd-udevd(8)](https://man7.org/linux/man-pages/man8/systemd-udevd.8.html)
### [udev(7)](https://man7.org/linux/man-pages/man7/udev.7.html)