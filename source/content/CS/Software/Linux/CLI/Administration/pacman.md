pacman es el gestor de paquetes oficial de Arch Linux y derivadas. Combina un formato binario de paquete sencillo con un sistema de compilación (`makepkg`) y permite instalar, actualizar, eliminar y consultar paquetes desde los repositorios oficiales o desde archivos locales/remotos. Cada invocación opera bajo una de sus operaciones principales (`-S`, `-R`, `-Q`, `-U`, `-F`, `-D`, `-T`), seleccionada por la primera flag mayúscula.

Relacionado: [[makepkg]], [[paru]].

# Operaciones principales

Una sola operación por invocación. Determina el resto de flags válidas.

| **Flag**            | **Descripción**                                          | **Ejemplo**          |
| ------------------- | -------------------------------------------------------- | -------------------- |
| `-S`, `--sync`      | Sincroniza/instala paquetes desde los repositorios.      | `pacman -S firefox`  |
| `-R`, `--remove`    | Elimina paquetes del sistema.                            | `pacman -R firefox`  |
| `-Q`, `--query`     | Consulta la base de datos local (paquetes instalados).   | `pacman -Q`          |
| `-U`, `--upgrade`   | Instala un paquete desde un archivo local o URL.         | `pacman -U pkg.zst`  |
| `-F`, `--files`     | Consulta la base de datos de archivos de los repos.      | `pacman -F vmlinuz`  |
| `-D`, `--database`  | Modifica la base de datos local (estado de paquetes).    | `pacman -D --asdeps glibc` |
| `-T`, `--deptest`   | Comprueba dependencias; imprime las no satisfechas.      | `pacman -T qt6-base` |
| `-V`, `--version`   | Muestra versión y licencia.                              | `pacman -V`          |
| `-h`, `--help`      | Ayuda (contextual a la operación indicada).             | `pacman -Sh`         |

# Opciones generales

Válidas en (casi) cualquier operación.

| **Flag**                       | **Descripción**                                                      | **Ejemplo**                              |
| ------------------------------ | ------------------------------------------------------------------- | ---------------------------------------- |
| `-b`, `--dbpath <ruta>`        | Ruta alternativa a la base de datos.                                | `pacman -Qb /var/lib/pacman`             |
| `-r`, `--root <ruta>`          | Directorio raíz de instalación alternativo.                         | `pacman -S -r /mnt base`                 |
| `-v`, `--verbose`              | Salida detallada (muestra rutas y configuración).                   | `pacman -Sv firefox`                     |
| `--arch <arch>`                | Sobreescribe la arquitectura configurada.                           | `pacman -S --arch x86_64 vim`            |
| `--cachedir <ruta>`            | Directorio de caché de paquetes alternativo.                        | `pacman -S --cachedir /tmp/cache vim`    |
| `--color <when>`               | Colorea la salida: `always`, `never`, `auto`.                       | `pacman -Ss --color never python`        |
| `--config <archivo>`           | Archivo de configuración alternativo.                               | `pacman -Sy --config /tmp/pacman.conf`   |
| `--debug`                      | Mensajes de depuración (acepta nivel numérico).                     | `pacman -Sy --debug`                     |
| `--gpgdir <ruta>`              | Directorio de GnuPG alternativo para verificación de firmas.        | `pacman -U --gpgdir /etc/pacman.d/gnupg pkg.zst` |
| `--hookdir <ruta>`             | Directorio adicional de hooks de transacción.                       | `pacman -S --hookdir /etc/pacman.d/hooks vim` |
| `--logfile <archivo>`          | Archivo de log alternativo.                                         | `pacman -S --logfile /tmp/pac.log vim`   |
| `--noconfirm`                  | Asume "sí" a todas las preguntas (no interactivo).                  | `pacman -S --noconfirm vim`              |
| `--confirm`                    | Cancela un `--noconfirm` previo (fuerza confirmación).             | `pacman -S --confirm vim`                |
| `--sysroot <dir>`              | Opera dentro de un sysroot (chroot ligero) antes de ejecutar.       | `pacman --sysroot /mnt -Syu`             |
| `--disable-download-timeout`   | Desactiva el timeout/velocidad mínima de descarga.                  | `pacman -Syu --disable-download-timeout` |

# Opciones de transacción

Comunes a `-S`, `-R` y `-U`.

| **Flag**                          | **Descripción**                                                       | **Ejemplo**                              |
| --------------------------------- | --------------------------------------------------------------------- | ---------------------------------------- |
| `-d`, `--nodeps`                  | Omite la comprobación de dependencias (`-dd` omite también versiones).| `pacman -Rdd glibc`                      |
| `--assume-installed <pkg=ver>`    | Trata una dependencia como ya satisfecha sin instalarla.              | `pacman -S --assume-installed bash=5.2 x`|
| `--dbonly`                        | Modifica solo la base de datos, no toca archivos en disco.            | `pacman -Rdd --dbonly paquete`           |
| `--noprogressbar`                 | No muestra barra de progreso (útil en scripts/logs).                  | `pacman -S --noprogressbar vim`          |
| `--noscriptlet`                   | No ejecuta los scriptlets `install`/`upgrade` del paquete.            | `pacman -U --noscriptlet pkg.zst`        |
| `-p`, `--print`                   | Solo imprime los objetivos (no ejecuta la transacción).              | `pacman -Sp firefox`                     |
| `--print-format <formato>`        | Formato de la salida de `--print` (`%n`, `%v`, `%l`, `%r`, `%s`).     | `pacman -Sp --print-format "%n-%v" vim`  |

# Opciones de instalación/actualización

Comunes a `-S` y `-U` (afectan a cómo se instalan los paquetes).

| **Flag**                       | **Descripción**                                                          | **Ejemplo**                               |
| ------------------------------ | ------------------------------------------------------------------------ | ----------------------------------------- |
| `--asdeps`                     | Marca los paquetes instalados como dependencias.                         | `pacman -S --asdeps libfoo`               |
| `--asexplicit`                 | Marca los paquetes instalados como explícitos.                           | `pacman -S --asexplicit libfoo`           |
| `--needed`                     | No reinstala paquetes ya actualizados.                                   | `pacman -S --needed base-devel`           |
| `--ignore <pkg>`               | Excluye un paquete de la actualización.                                  | `pacman -Syu --ignore linux`              |
| `--ignoregroup <grupo>`        | Excluye un grupo entero de la actualización.                             | `pacman -Syu --ignoregroup gnome`         |
| `--overwrite <glob>`           | Sobrescribe archivos en conflicto que coincidan con el patrón.           | `pacman -S --overwrite "/usr/lib/*" pkg`  |
| `--downloadonly` / `-w`        | Solo descarga, no instala (ver `-S`).                                    | `pacman -Sw firefox`                      |

# -S / --sync (repositorios)

Instala, busca y actualiza desde los repositorios remotos.

| **Flag**               | **Descripción**                                                            | **Ejemplo**             |
| ---------------------- | -------------------------------------------------------------------------- | ----------------------- |
| `-y`, `--refresh`      | Refresca las bases de datos de paquetes (`-yy` fuerza aunque estén al día).| `pacman -Syy`           |
| `-u`, `--sysupgrade`   | Actualiza todo el sistema (`-uu` permite downgrades).                      | `pacman -Syu`           |
| `-s`, `--search <re>`  | Busca paquetes por expresión regular en nombre y descripción.             | `pacman -Ss "^python-"` |
| `-i`, `--info`         | Muestra información del paquete (`-ii` añade ficheros de backup).          | `pacman -Si firefox`    |
| `-l`, `--list <repo>`  | Lista todos los paquetes de un repositorio.                               | `pacman -Sl extra`      |
| `-g`, `--groups`       | Lista grupos disponibles o los miembros de un grupo.                       | `pacman -Sg base-devel` |
| `-c`, `--clean`        | Limpia la caché de paquetes (`-cc` elimina toda la caché).                | `pacman -Sc`            |
| `-q`, `--quiet`        | Salida reducida (solo nombres, sin versiones/repos).                       | `pacman -Ssq vim`       |
| `-w`, `--downloadonly` | Descarga los paquetes a la caché sin instalarlos.                          | `pacman -Sw firefox`    |

# -R / --remove (eliminación)

Desinstala paquetes y, opcionalmente, sus dependencias.

| **Flag**             | **Descripción**                                                              | **Ejemplo**          |
| -------------------- | ---------------------------------------------------------------------------- | -------------------- |
| `-s`, `--recursive`  | Elimina también dependencias no requeridas por otros (`-ss` más agresivo).   | `pacman -Rs paquete` |
| `-c`, `--cascade`    | Elimina el paquete y todo lo que dependa de él.                              | `pacman -Rc paquete` |
| `-n`, `--nosave`     | Elimina también los archivos de configuración (no guarda `.pacsave`).        | `pacman -Rn paquete` |
| `-u`, `--unneeded`   | Elimina solo objetivos que no son requeridos por otros paquetes.             | `pacman -Ru paquete` |

# -Q / --query (base de datos local)

Consulta los paquetes ya instalados.

| **Flag**                | **Descripción**                                                          | **Ejemplo**             |
| ----------------------- | ------------------------------------------------------------------------ | ----------------------- |
| `-s`, `--search <re>`   | Busca entre los paquetes instalados.                                     | `pacman -Qs python`     |
| `-i`, `--info`          | Muestra información del paquete (`-ii` añade archivos de backup).        | `pacman -Qi firefox`    |
| `-l`, `--list`          | Lista los archivos que posee el paquete.                                 | `pacman -Ql firefox`    |
| `-o`, `--owns <archivo>`| Indica qué paquete posee un archivo dado.                                | `pacman -Qo /usr/bin/ls`|
| `-p`, `--file <archivo>`| Consulta un archivo de paquete local en lugar de la base de datos.       | `pacman -Qpl pkg.zst`   |
| `-c`, `--changelog`     | Muestra el changelog del paquete.                                        | `pacman -Qc linux`      |
| `-k`, `--check`         | Verifica que los archivos del paquete existen (`-kk` comprueba propiedades).| `pacman -Qkk firefox` |
| `-e`, `--explicit`      | Lista paquetes instalados explícitamente.                                | `pacman -Qe`            |
| `-d`, `--deps`          | Lista paquetes instalados como dependencias.                             | `pacman -Qd`            |
| `-m`, `--foreign`       | Lista paquetes ajenos a los repos (típicamente del AUR).                 | `pacman -Qm`            |
| `-n`, `--native`        | Lista paquetes encontrados en los repos sincronizados.                   | `pacman -Qn`            |
| `-t`, `--unrequired`    | Lista paquetes no requeridos por ningún otro (`-tt` ignora opcionales).  | `pacman -Qtdq`          |
| `-u`, `--upgrades`      | Lista paquetes con actualización pendiente.                              | `pacman -Qu`            |
| `-g`, `--groups`        | Lista grupos y sus miembros instalados.                                  | `pacman -Qg`            |
| `-q`, `--quiet`         | Salida reducida (solo nombres).                                          | `pacman -Qq`            |

# -U / --upgrade (archivo local/remoto)

Instala un paquete desde un `.pkg.tar.zst` local o una URL. Admite las opciones de transacción e instalación.

| **Flag**          | **Descripción**                                       | **Ejemplo**                                |
| ----------------- | ----------------------------------------------------- | ------------------------------------------ |
| `(objetivo)`      | Ruta local o URL del archivo de paquete.              | `pacman -U ./vim-9.1.pkg.tar.zst`          |
| `--asdeps`        | Marca el paquete instalado como dependencia.          | `pacman -U --asdeps pkg.zst`               |
| `--needed`        | No reinstala si ya está a esa versión.                | `pacman -U --needed pkg.zst`               |
| `--overwrite`     | Sobrescribe archivos en conflicto.                    | `pacman -U --overwrite "*" pkg.zst`        |

# -F / --files (base de datos de archivos)

Consulta qué paquete provee un archivo, usando la base de datos de archivos de los repos.

| **Flag**               | **Descripción**                                                       | **Ejemplo**                  |
| ---------------------- | --------------------------------------------------------------------- | ---------------------------- |
| `-y`, `--refresh`      | Descarga/actualiza la base de datos de archivos (`-yy` fuerza).       | `pacman -Fy`                 |
| `-s`, `--search`       | Busca el paquete que contiene un archivo (por nombre).                | `pacman -Fs vmlinuz`         |
| `-x`, `--regex`        | Interpreta el patrón de búsqueda como expresión regular.             | `pacman -Fx "bin/system.*"`  |
| `-l`, `--list`         | Lista los archivos que provee un paquete (desde el repo).            | `pacman -Fl linux`           |
| `-o`, `--owns`         | Muestra el paquete propietario de un archivo (consulta remota).     | `pacman -Fo /usr/bin/vim`    |
| `-q`, `--quiet`        | Salida reducida.                                                     | `pacman -Fsq vmlinuz`        |
| `--machinereadable`    | Salida en formato parseable por máquina.                            | `pacman -Fs --machinereadable ls` |

# -D / --database (estado en BD local)

Modifica metadatos de paquetes ya instalados sin tocar archivos.

| **Flag**          | **Descripción**                                                  | **Ejemplo**                  |
| ----------------- | ---------------------------------------------------------------- | ---------------------------- |
| `--asdeps <pkg>`  | Marca el paquete como dependencia.                               | `pacman -D --asdeps libfoo`  |
| `--asexplicit <pkg>` | Marca el paquete como instalado explícitamente.              | `pacman -D --asexplicit gcc` |
| `-k`, `--check`   | Verifica la consistencia de la base de datos (`-kk` más estricto).| `pacman -Dkk`              |
| `-q`, `--quiet`   | Suprime mensajes de éxito.                                       | `pacman -D --quiet --asdeps libfoo` |

# -T / --deptest (test de dependencias)

Comprueba si una lista de dependencias está satisfecha; imprime las que faltan y devuelve código de salida distinto de 0 si hay alguna. Útil en scripts de compilación.

| **Flag**       | **Descripción**                                              | **Ejemplo**                  |
| -------------- | ------------------------------------------------------------ | ---------------------------- |
| `(objetivo)`   | Dependencia(s) a comprobar (acepta restricciones de versión).| `pacman -T "qt6-base>=6.5"`  |

# Notas operativas

- **No** usar `-Sy paquete` sin `-u`: refresca la BD sin actualizar el sistema, dejando un estado parcial propenso a roturas. Usar siempre `pacman -Syu`.
- Limpieza de huérfanos: `pacman -Qtdq | pacman -Rns -` elimina dependencias no requeridas.
- Archivos `.pacnew` / `.pacsave`: tras una actualización, revisar con `pacdiff` (paquete `pacman-contrib`).
- El AUR no se gestiona con pacman; usar un helper como [[paru]].
