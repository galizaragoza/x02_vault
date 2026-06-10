**asmcmd** (ASM Command-Line utility) es la herramienta de línea de comandos de **Oracle Automatic Storage Management** para administrar instancias ASM, grupos de discos (disk groups), archivos, alias, plantillas, volúmenes ADVM y control de acceso (ACL). Funciona como una shell interactiva o de forma no interactiva pasándole un subcomando. Requiere las variables de entorno `ORACLE_HOME` y `ORACLE_SID` apuntando a la instancia ASM (`+ASM`) y conexión con privilegio `SYSASM` o `SYSDBA`. En contexto de pentest es relevante para enumeración y post-explotación de infraestructuras Oracle sobre ASM.

```
asmcmd [-V] [-v] [--privilege priv] [-p] [comando [args]]
```

## Opciones de invocación

| **Flag** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-V` | Imprime la versión de `asmcmd` y termina. | `asmcmd -V` |
| `-v` | Modo verboso: muestra información de depuración y trazas de error. | `asmcmd -v lsdg` |
| `-p` | Incluye el directorio ASM actual en el prompt interactivo. | `asmcmd -p` |
| `--privilege priv` | Fija el privilegio de conexión: `sysasm` (por defecto) o `sysdba`. | `asmcmd --privilege sysdba` |
| `help [comando]` | Muestra la ayuda general o de un subcomando concreto. | `help mkdg` |
| `exit` / `quit` | Sale de la shell interactiva. | `exit` |

> Modo no interactivo: cualquier subcomando puede ejecutarse directamente desde la shell del sistema, p. ej. `asmcmd lsdg`, ideal para scripting.

## Gestión de archivos y directorios

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `cd dir` | Cambia el directorio ASM actual. | `cd +DATA/orcl` |
| `pwd` | Muestra el directorio ASM actual. | `pwd` |
| `ls [opts] [patrón]` | Lista contenido. Opts: `-l` largo, `-a` ruta del alias, `-d` solo info del dir, `-s` estadísticas I/O, `-t` ordena por fecha, `-r` invierte, `-H` sin cabecera, `-g` info de GV$ (RAC), `-c` selecciona V$ASM_DISKGROUP, `--permission` muestra ACL. | `ls -l +DATA/orcl/datafile` |
| `lsof [-G dg] [--instance i]` | Lista los archivos abiertos por clientes. | `lsof -G DATA` |
| `du [-H] [dir]` | Uso de espacio total/usado del árbol indicado (`-H` sin cabecera). | `du +DATA/orcl` |
| `find [-t tipo] dir patrón` | Busca archivos/alias bajo un directorio; `-t` filtra por tipo (DATAFILE, etc.). | `find -t DATAFILE +DATA *` |
| `cp [-i] [conn] origen destino` | Copia archivos entre ASM y el SO o entre instancias (`-i` interactivo; *conn* = `user@host.port.SID:`). | `cp +DATA/orcl/spfile.ora /tmp/spfile.ora` |
| `mkdir dir` | Crea un directorio. | `mkdir +DATA/backups` |
| `rm [-r] [-f] objeto` | Borra archivos/dirs/alias (`-r` recursivo, `-f` forzado). | `rm -rf +DATA/old` |
| `mkalias archivo alias` | Crea un alias (nombre legible) para un archivo de sistema. | `mkalias +DATA/orcl/datafile/sys.259 +DATA/orcl/sys.dbf` |
| `rmalias [-r] alias` | Elimina un alias sin borrar el archivo subyacente (`-r` recursivo). | `rmalias +DATA/orcl/sys.dbf` |

## Gestión de grupos de discos (Disk Groups)

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `lsdg [-g] [--discovery] [-H] [dg]` | Lista grupos de discos y su estado/espacio (`-g` GV$ en RAC, `--discovery` desde discos descubiertos, `-H` sin cabecera). | `lsdg -H DATA` |
| `lsdsk [opts] [patrón]` | Lista discos. Opts: `-k` espacio, `-s` estadísticas I/O, `-p` info de path, `-t` fechas de creación, `-d dg` filtra por grupo, `-g` GV$, `-c` V$, `-H` sin cabecera, `-I` no usar caché, `--discovery`, `--candidate`, `--member`, `--suppressheader`, `--statistics`. | `lsdsk -k -d DATA` |
| `lsod [-G dg] [--process p] [patrón]` | Lista los discos abiertos (open disks) y los procesos que los usan. | `lsod -G DATA` |
| `mkdg config.xml` | Crea un grupo de discos a partir de un archivo de configuración XML. | `mkdg mydg.xml` |
| `chdg config.xml` | Modifica un grupo (añade/quita discos, rebalanceo) desde XML. | `chdg altdg.xml` |
| `dropdg [-r] [-f] dg` | Elimina un grupo de discos (`-r` incluye contenido, `-f` forzado). | `dropdg -r -f TESTDG` |
| `chkdg [--repair] dg` | Verifica la consistencia de metadatos del grupo (`--repair` corrige). | `chkdg --repair DATA` |
| `mount [--restrict] [-a \| dg]` | Monta uno o todos (`-a`) los grupos (`--restrict` modo restringido). | `mount -a` |
| `umount [-a \| dg] [-f]` | Desmonta uno o todos los grupos (`-f` forzado). | `umount DATA` |
| `online [-a] -G dg [-D disco]` | Pone discos online tras una caída (`-a` todos). | `online -G DATA -D DATA_0001` |
| `offline -G dg {-D disco \| -F failgroup} [-t tiempo]` | Pone discos/failgroups offline (`-t` ventana de reparación). | `offline -G DATA -D DATA_0001` |
| `rebal [--power n] [--wait] dg` | Inicia un rebalanceo manual (`--power` 0-1024, `--wait` bloquea hasta terminar). | `rebal --power 4 DATA` |
| `remap dg disco rango_bloques` | Repara bloques físicos dañados copiando desde una copia redundante. | `remap DATA DATA_0001 5000-5240` |

## Atributos de grupos de discos

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `lsattr [-l] [-m] [-G dg] [-H] [patrón]` | Lista atributos del grupo (`-l` muestra nombres, `-m` atributos heredables del template, `-H` sin cabecera). | `lsattr -lm -G DATA` |
| `setattr -G dg atributo valor` | Fija el valor de un atributo del grupo. | `setattr -G DATA compatible.asm 19.0.0.0.0` |

## Gestión de plantillas (Templates)

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `lstmpl [-l] [-G dg] [-H] [patrón]` | Lista las plantillas de un grupo (`-l` largo, `-H` sin cabecera). | `lstmpl -l -G DATA` |
| `mktmpl -G dg [atributos] plantilla` | Crea una plantilla (redundancia, striping, etc.). | `mktmpl -G DATA --redundancy high mytmpl` |
| `chtmpl -G dg [atributos] plantilla` | Modifica los atributos de una plantilla. | `chtmpl -G DATA --primary hot mytmpl` |
| `rmtmpl -G dg plantilla` | Elimina una plantilla. | `rmtmpl -G DATA mytmpl` |

## Control de acceso a archivos (ACL)

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `lsusr [-a] [-G dg] [-H] [patrón]` | Lista usuarios del grupo (`-a` todos los grupos). | `lsusr -G DATA` |
| `mkusr dg usuario` | Añade un usuario del SO al grupo de discos. | `mkusr DATA oracle` |
| `rmusr [-r] dg usuario` | Elimina un usuario del grupo. | `rmusr DATA oracle` |
| `passwd usuario` | Cambia la contraseña de un usuario ASM. | `passwd asmadmin` |
| `lsgrp [-a] [-G dg] [-H] [patrón]` | Lista los grupos de usuarios (user groups). | `lsgrp -G DATA` |
| `mkgrp dg grupo [usuario ...]` | Crea un grupo de usuarios y añade miembros. | `mkgrp DATA dba oracle grid` |
| `rmgrp dg grupo` | Elimina un grupo de usuarios. | `rmgrp DATA dba` |
| `grpmod {--add \| --delete} dg grupo usuario ...` | Añade o quita miembros de un grupo. | `grpmod --add DATA dba oracle` |
| `groups dg usuario` | Lista los grupos a los que pertenece un usuario. | `groups DATA oracle` |
| `chown usuario\|num archivo ...` | Cambia el propietario de archivos ASM. | `chown oracle +DATA/orcl/test.dbf` |
| `chgrp grupo archivo ...` | Cambia el grupo de archivos ASM. | `chgrp dba +DATA/orcl/test.dbf` |
| `chmod modo archivo ...` | Cambia permisos (`ugo+rw`, `600`, etc.) de archivos ASM. | `chmod 640 +DATA/orcl/test.dbf` |

## Gestión de volúmenes (ASM Dynamic Volume Manager - ADVM)

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `volcreate -G dg -s tam [atributos] volumen` | Crea un volumen ADVM (atributos: `--column`, `--width`, `--redundancy`, `--primary`, `--secondary`, `--stripe_columns`, `--stripe_width`). | `volcreate -G DATA -s 10G --redundancy high vol1` |
| `volinfo {-a \| -G dg vol \| --show_diskgroup dev \| --show_volume dev}` | Muestra información de volúmenes. | `volinfo -G DATA vol1` |
| `volresize -G dg -s tam [-f] volumen` | Redimensiona un volumen (`-f` fuerza reducción). | `volresize -G DATA -s 20G vol1` |
| `volset -G dg [atributos] volumen` | Modifica atributos de un volumen (p. ej. `--usagestring`, `--mountpath`). | `volset -G DATA --mountpath /acfs vol1` |
| `volenable {-a \| -G dg volumen}` | Habilita (activa el dispositivo de) un volumen. | `volenable -G DATA vol1` |
| `voldisable {-a \| -G dg volumen}` | Deshabilita un volumen sin borrarlo. | `voldisable -G DATA vol1` |
| `voldelete -G dg volumen` | Elimina un volumen. | `voldelete -G DATA vol1` |
| `volstat [-G dg] [volumen]` | Muestra estadísticas I/O de los volúmenes. | `volstat -G DATA` |

## Gestión de grupos de archivos y cuotas (File Groups / Quota - Flex Disk Groups)

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `lsfg [-G dg] [--filegroup fg] [patrón]` | Lista los grupos de archivos de un disk group flex. | `lsfg -G DATA` |
| `mkfg config.xml` | Crea un grupo de archivos desde XML. | `mkfg fg.xml` |
| `chfg config.xml` | Modifica un grupo de archivos desde XML. | `chfg fg.xml` |
| `mvfg -G dg --filegroup fg quotagroup` | Mueve un grupo de archivos a otro grupo de cuota. | `mvfg -G DATA --filegroup fg1 qg1` |
| `rmfg -G dg --filegroup fg` | Elimina un grupo de archivos. | `rmfg -G DATA --filegroup fg1` |
| `mvfile archivo --filegroup fg` | Mueve un archivo a un grupo de archivos. | `mvfile +DATA/orcl/x.dbf --filegroup fg1` |
| `lsqg [-G dg] [--quotagroup qg] [patrón]` | Lista los grupos de cuota. | `lsqg -G DATA` |
| `mkqg -G dg --quotagroup qg [quota]` | Crea un grupo de cuota con su límite. | `mkqg -G DATA --quotagroup qg1 quota 100G` |
| `chqg -G dg --quotagroup qg {quota q \| --filegroup fg}` | Modifica la cuota o asigna un file group. | `chqg -G DATA --quotagroup qg1 quota 200G` |
| `mvqg -G dg --filegroup fg quotagroup` | Reasigna un file group a otro grupo de cuota. | `mvqg -G DATA --filegroup fg1 qg2` |
| `rmqg -G dg --quotagroup qg` | Elimina un grupo de cuota. | `rmqg -G DATA --quotagroup qg1` |

## Gestión de la instancia ASM

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `startup [--nomount] [--restrict] [--mount] [--open] [--pfile p]` | Arranca la instancia ASM en el estado indicado. | `asmcmd startup --mount` |
| `shutdown [--abort \| --immediate \| --normal]` | Detiene la instancia ASM. | `asmcmd shutdown --immediate` |
| `lsct [-g] [-H] [dg]` | Lista los clientes (bases de datos) conectados a ASM. | `lsct -H DATA` |
| `lsop` | Lista las operaciones en curso (rebalanceos, etc.) en las instancias. | `lsop` |
| `iostat [-et] [-g] [-G dg] [-H] [--io] [--region] [intervalo]` | Estadísticas de I/O por disco (`-e` errores, `-t` tiempos, `--io` en operaciones en vez de bytes). | `iostat -et -G DATA 5` |
| `dsget` | Muestra la cadena de descubrimiento de discos (ASM_DISKSTRING). | `dsget` |
| `dsset [--normal\|--profile\|--parameter] cadena` | Fija la cadena de descubrimiento de discos. | `dsset '/dev/oracleasm/disks/*'` |

## Gestión del SPFILE de la instancia

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `spget` | Muestra la ubicación del SPFILE registrada en el GPnP profile. | `spget` |
| `spset ubicación` | Fija la ubicación del SPFILE en el GPnP profile. | `spset +DATA/asm/asmparameterfile/spfile.ora` |
| `spcopy [-u] origen destino` | Copia el SPFILE entre ubicaciones (`-u` actualiza el GPnP profile). | `spcopy -u /tmp/spfile.ora +DATA/spfileASM.ora` |
| `spmove origen destino` | Mueve el SPFILE y actualiza el GPnP profile. | `spmove +DATA/old.ora +DATA/new.ora` |
| `spbackup origen destino` | Crea una copia de respaldo del SPFILE (sin actualizar el profile). | `spbackup +DATA/spfileASM.ora /tmp/spfile.bak` |

## Gestión del archivo de contraseñas (Password File)

| **Subcomando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `pwget {--asm \| --dbuniquename nombre}` | Muestra la ubicación del password file de ASM o de una BD. | `pwget --asm` |
| `pwset {--asm \| --dbuniquename n} ubicación` | Registra la ubicación del password file en el GPnP profile. | `pwset --asm +DATA/orapwasm` |
| `pwcreate {--asm \| --dbuniquename n} [-f] ubicación pass` | Crea un password file (`-f` sobrescribe). | `pwcreate --asm +DATA/orapwasm MiPass#1` |
| `pwcopy [-f] origen destino` | Copia un password file (`-f` sobrescribe). | `pwcopy +DATA/orapwasm /tmp/orapwasm` |
| `pwmove {--asm \| --dbuniquename n} origen destino` | Mueve un password file y actualiza el profile. | `pwmove --asm +DATA/old +DATA/new` |
| `pwdelete {--asm \| --dbuniquename n} ubicación` | Elimina un password file y limpia su referencia. | `pwdelete --asm +DATA/orapwasm` |
| `lspwusr [--asm \| --dbuniquename n]` | Lista los usuarios del password file. | `lspwusr --asm` |
| `orapwusr {--add \| --modify \| --delete} [--privilege p] usuario` | Añade, modifica o borra usuarios del password file (`--privilege sysasm\|sysdba\|sysoper`). | `orapwusr --add --privilege sysasm admin2` |

Relacionado: [[docker-cli]], [[searchsploit]].
