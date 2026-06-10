paru es un *AUR helper* escrito en Rust que envuelve a [[pacman]] añadiendo soporte para el *Arch User Repository* (AUR). Replica la interfaz de pacman (mismas operaciones y flags) y delega en él todo lo relativo a repos oficiales, interceptando solo los objetivos del AUR para clonar, revisar, compilar (vía [[makepkg]]) e instalar. Aporta además operaciones propias (`-P`, `-G`, `-U`, `-B`, `-C`, `-L`) y un amplio conjunto de opciones de control de la compilación, la revisión de PKGBUILD y los paquetes de desarrollo (`-git`).

Al no recibir operación, `paru` por defecto realiza un *sysupgrade* interactivo (equivalente a `paru -Syu` de repos + AUR).

Relacionado: [[pacman]], [[makepkg]].

# Operaciones principales

Hereda las de pacman y añade las propias. Una por invocación.

| **Flag**              | **Descripción**                                                       | **Ejemplo**            |
| --------------------- | --------------------------------------------------------------------- | ---------------------- |
| *(sin operación)*     | Sysupgrade interactivo de repos + AUR.                                | `paru`                 |
| `-S`, `--sync`        | Instala/sincroniza paquetes de repos y/o AUR.                         | `paru -S yay`          |
| `-R`, `--remove`      | Elimina paquetes (delega en pacman).                                  | `paru -Rns yay`        |
| `-Q`, `--query`       | Consulta la base de datos local (delega en pacman).                   | `paru -Qm`             |
| `-U`, `--upgrade`     | Construye/instala desde un PKGBUILD o archivo local.                  | `paru -U ./pkg`        |
| `-F`, `--files`       | Consulta la base de datos de archivos (delega en pacman).             | `paru -Fy vmlinuz`     |
| `-D`, `--database`    | Modifica la base de datos local (delega en pacman).                   | `paru -D --asdeps x`   |
| `-T`, `--deptest`     | Test de dependencias (delega en pacman).                              | `paru -T "qt6-base"`   |
| `-P`, `--show`        | Operaciones de información de paru (estadísticas, noticias…).         | `paru -Pw`             |
| `-G`, `--getpkgbuild` | Descarga los archivos de compilación de un paquete (repo o AUR).      | `paru -G yay`          |
| `-B`, `--build`       | Compila e instala un PKGBUILD desde un directorio local.             | `paru -B ./mipaquete`  |
| `-C`, `--chrootctl`   | Control interactivo del chroot de compilación.                       | `paru -C`              |
| `-L`, `--repoctl`     | Control del repositorio local de paru.                               | `paru -L`              |
| `-V`, `--version`     | Versión.                                                              | `paru -V`              |
| `-h`, `--help`        | Ayuda (contextual a la operación).                                   | `paru -Sh`            |

# Selección de origen (AUR vs repos)

Filtran sobre qué fuente actúa la operación. Sin ellas, paru opera sobre ambas.

| **Flag**          | **Descripción**                                          | **Ejemplo**          |
| ----------------- | -------------------------------------------------------- | -------------------- |
| `-a`, `--aur`     | Opera únicamente sobre el AUR.                           | `paru -Sua`          |
| `--repo`          | Opera únicamente sobre los repositorios oficiales.       | `paru -S --repo vim` |
| `--mode <m>`      | Fija el modo: `aur`, `repo` o `any`.                    | `paru --mode aur -Ss vim` |

# -S / --sync (AUR + repos)

Extiende `pacman -S`. Las flags de pacman (`-y`, `-u`, `-s`, `-i`, `-c`, `-w`, `-q`, `-g`, `-l`…) siguen siendo válidas; aquí se listan las añadidas o con comportamiento ampliado por paru.

| **Flag**              | **Descripción**                                                          | **Ejemplo**            |
| --------------------- | ------------------------------------------------------------------------ | ---------------------- |
| `-y`, `--refresh`     | Refresca repos; con AUR fuerza re-clonado/fetch de los PKGBUILD.         | `paru -Syu`            |
| `-u`, `--sysupgrade`  | Actualiza repos y AUR. `-u` doble incluye paquetes `-git` (devel).       | `paru -Sua`            |
| `-s`, `--search`      | Busca también en el AUR (`-ss` ordena/expande resultados).               | `paru -Ss spotify`     |
| `-i`, `--info`        | Muestra info del paquete del AUR (votos, popularidad, mantenedor).       | `paru -Si yay`         |
| `-c`, `--clean`       | Limpia caché de pacman y, además, los clones de AUR no instalados.       | `paru -Sc`             |
| `-w`, `--downloadonly`| Solo descarga/clona y compila sin instalar.                             | `paru -Sw yay`         |
| `--aururl <url>`      | URL base del AUR a utilizar.                                             | `paru -S --aururl https://aur.archlinux.org yay` |
| `--clonedir <ruta>`  | Directorio donde se clonan los paquetes del AUR.                         | `paru -S --clonedir ~/.cache/paru/clone yay` |

# Control de compilación (makepkg / git / gpg)

Opciones que paru pasa a las herramientas subyacentes durante la construcción de paquetes AUR.

| **Flag**                      | **Descripción**                                                  | **Ejemplo**                                  |
| ----------------------------- | ---------------------------------------------------------------- | -------------------------------------------- |
| `--makepkg <ruta>`            | Binario de makepkg a usar.                                       | `paru -S --makepkg /usr/bin/makepkg yay`     |
| `--mflags <flags>`            | Flags adicionales pasadas a makepkg.                            | `paru -S --mflags "--skipchecksums" yay`     |
| `--makepkgconf <archivo>`     | Archivo `makepkg.conf` alternativo.                            | `paru -S --makepkgconf ./makepkg.conf yay`   |
| `--pacman <ruta>`             | Binario de pacman a usar.                                       | `paru -S --pacman /usr/bin/pacman yay`       |
| `--git <ruta>`                | Binario de git a usar.                                          | `paru -S --git /usr/bin/git yay`             |
| `--gitflags <flags>`          | Flags adicionales para git.                                     | `paru -S --gitflags "--depth=1" yay`         |
| `--gpg <ruta>`                | Binario de gpg a usar.                                          | `paru -S --gpg /usr/bin/gpg yay`             |
| `--gpgflags <flags>`          | Flags adicionales para gpg.                                     | `paru -S --gpgflags "--keyserver hkps://..." yay` |
| `--sudo <ruta>`               | Binario de elevación de privilegios (p. ej. `doas`).           | `paru -S --sudo /usr/bin/doas yay`           |
| `--sudoflags <flags>`         | Flags adicionales para el binario de sudo.                      | `paru -S --sudoflags "-A" yay`               |
| `--asp <ruta>`                | Binario de `asp`/`pkgctl` para obtener fuentes de repos.        | `paru -G --asp /usr/bin/pkgctl linux`        |

# Revisión y diff de PKGBUILD

Controlan cómo se inspecciona el código antes de compilar (defensa frente a PKGBUILD maliciosos).

| **Flag**                  | **Descripción**                                                         | **Ejemplo**               |
| ------------------------- | ----------------------------------------------------------------------- | ------------------------- |
| `--review`                | Fuerza la revisión interactiva de cada PKGBUILD antes de compilar.      | `paru -S --review yay`    |
| `--skipreview`            | Omite la revisión de PKGBUILD.                                          | `paru -S --skipreview yay`|
| `--upgrademenu`           | Muestra menú para elegir qué paquetes actualizar.                       | `paru -Sua --upgrademenu` |
| `--noupgrademenu`         | Desactiva el menú de actualización.                                    | `paru -Sua --noupgrademenu` |
| `--nodiffmenu`            | No muestra el menú de diffs.                                           | `paru -S --nodiffmenu yay`|
| `--diffmenu`              | Muestra el menú de diffs entre versiones del PKGBUILD.                 | `paru -S --diffmenu yay`  |
| `--pgpfetch`              | Recupera automáticamente claves PGP necesarias.                        | `paru -S --pgpfetch yay`  |
| `--nopgpfetch`            | No recupera claves PGP automáticamente.                                | `paru -S --nopgpfetch yay`|
| `--useask`                | Usa el flag `--ask` de pacman para resolver conflictos.                | `paru -S --useask yay`    |
| `--nouseask`              | No usa `--ask`; pregunta cada conflicto.                               | `paru -S --nouseask yay`  |
| `--fm <programa>`         | Gestor de archivos para revisar el PKGBUILD visualmente.               | `paru -S --fm nnn yay`    |
| `--fmflags <flags>`       | Flags para el gestor de archivos de revisión.                          | `paru -S --fmflags "-a" yay` |
| `--bat <ruta>`            | Binario `bat` para colorear los diffs.                                 | `paru -S --bat /usr/bin/bat yay` |
| `--batflags <flags>`      | Flags para `bat`.                                                      | `paru -S --batflags "--style=plain" yay` |

# Paquetes de desarrollo (devel / -git)

Gestión de paquetes que compilan desde `HEAD` de un VCS.

| **Flag**                   | **Descripción**                                                       | **Ejemplo**               |
| -------------------------- | --------------------------------------------------------------------- | ------------------------- |
| `--devel`                  | Comprueba actualizaciones de paquetes `-git`/devel por commit.        | `paru -Sua --devel`       |
| `--nodevel`                | No comprueba paquetes de desarrollo.                                  | `paru -Sua --nodevel`     |
| `--gendb`                  | (Re)genera la base de datos de devel sin actualizar.                  | `paru --gendb`            |
| `--develsuffixes <sufijos>`| Sufijos que identifican un paquete devel (def. `-git -cvs -svn…`).   | `paru -Sua --develsuffixes "-git -hg"` |

# Reconstrucción y caché

Controlan re-descarga, recompilación y limpieza de artefactos.

| **Flag**            | **Descripción**                                                       | **Ejemplo**               |
| ------------------- | --------------------------------------------------------------------- | ------------------------- |
| `--rebuild`         | Recompila los objetivos aunque exista el paquete (`--rebuildall`, `--rebuildtree`). | `paru -S --rebuild yay` |
| `--norebuild`       | No recompila si el paquete ya existe.                                 | `paru -S --norebuild yay` |
| `--redownload`      | Vuelve a descargar las fuentes (`--redownloadall`).                  | `paru -S --redownload yay`|
| `--noredownload`    | No vuelve a descargar fuentes existentes.                            | `paru -S --noredownload yay` |
| `--cleanafter`      | Limpia el directorio de compilación tras instalar.                  | `paru -S --cleanafter yay`|
| `--nocleanafter`    | Conserva el directorio de compilación.                              | `paru -S --nocleanafter yay` |
| `--keepsrc`         | Conserva `$srcdir`/`$pkgdir` tras compilar.                         | `paru -S --keepsrc yay`   |
| `--removemake`      | Elimina las dependencias de compilación al terminar (`=ask`).        | `paru -S --removemake yay`|
| `--noremovemake`    | Conserva las dependencias de compilación.                           | `paru -S --noremovemake yay` |
| `--batchinstall`    | Instala todos los paquetes compilados en una sola transacción.       | `paru -S --batchinstall a b c` |
| `--nobatchinstall`  | Instala cada paquete por separado a medida que se compila.          | `paru -S --nobatchinstall yay` |

# Resolución y comportamiento

| **Flag**             | **Descripción**                                                       | **Ejemplo**               |
| -------------------- | --------------------------------------------------------------------- | ------------------------- |
| `--provides`         | Considera paquetes que proveen el objetivo al resolver.              | `paru -S --provides cmd`  |
| `--noprovides`       | No tiene en cuenta los `provides`.                                  | `paru -S --noprovides cmd`|
| `--combinedupgrade`  | Combina la actualización de repos y AUR en una transacción.         | `paru -Sua --combinedupgrade` |
| `--nocombinedupgrade`| Actualiza repos y AUR por separado.                                | `paru -Sua --nocombinedupgrade` |
| `--sudoloop`         | Refresca el timestamp de sudo en bucle para no pedir contraseña.    | `paru -Sua --sudoloop`    |
| `--nosudoloop`       | Desactiva el bucle de sudo.                                         | `paru -Sua --nosudoloop`  |
| `--failfast`         | Aborta al primer paquete que falle al compilar.                    | `paru -S --failfast a b`  |
| `--nofailfast`       | Continúa con el resto aunque uno falle.                            | `paru -S --nofailfast a b`|
| `--newsonupgrade`    | Muestra las noticias de Arch antes de actualizar.                  | `paru -Sua --newsonupgrade` |
| `--nocheck`          | Omite las funciones `check()` de los PKGBUILD.                     | `paru -S --nocheck yay`   |
| `--installdebug`     | Instala también los paquetes de depuración generados.             | `paru -S --installdebug yay` |
| `--noinstalldebug`   | No instala paquetes de depuración.                                | `paru -S --noinstalldebug yay` |

# Firma y repositorio local

| **Flag**            | **Descripción**                                                       | **Ejemplo**                       |
| ------------------- | --------------------------------------------------------------------- | --------------------------------- |
| `--sign`            | Firma los paquetes generados con GPG (`--sign=clave`).               | `paru -S --sign yay`              |
| `--nosign`          | No firma los paquetes.                                               | `paru -S --nosign yay`            |
| `--signdb`          | Firma la base de datos del repositorio local (`--signdb=clave`).     | `paru -L --signdb`                |
| `--localrepo`       | Compila los paquetes hacia un repositorio local de pacman.          | `paru -S --localrepo yay`         |
| `--chroot`          | Compila dentro de un chroot limpio (aislamiento de dependencias).   | `paru -S --chroot yay`            |
| `--nochroot`        | No usa chroot; compila en el entorno del usuario.                   | `paru -S --nochroot yay`          |

# Ordenación y búsqueda

| **Flag**            | **Descripción**                                                       | **Ejemplo**                   |
| ------------------- | --------------------------------------------------------------------- | ----------------------------- |
| `--bottomup`        | Muestra los resultados de búsqueda de abajo hacia arriba (por defecto).| `paru -Ss --bottomup vim`    |
| `--topdown`         | Muestra los resultados de arriba hacia abajo.                        | `paru -Ss --topdown vim`      |
| `--sortby <campo>`  | Ordena por `votes`, `popularity`, `id`, `baseid`, `name`, `base`…    | `paru -Ss --sortby popularity vim` |
| `--searchby <campo>`| Busca por `name`, `name-desc`, `maintainer`, `depends`, `provides`…  | `paru -Ss --searchby maintainer foo` |
| `--limit <n>`       | Limita el número de resultados de búsqueda.                          | `paru -Ss --limit 10 vim`     |

# -P / --show (información de paru)

Operaciones de consulta propias de paru.

| **Flag**            | **Descripción**                                                       | **Ejemplo**          |
| ------------------- | --------------------------------------------------------------------- | -------------------- |
| `-c`, `--complete`  | Genera la lista de autocompletado de paquetes.                       | `paru -Pc`           |
| `-s`, `--stats`     | Estadísticas del sistema y de los paquetes instalados.              | `paru -Ps`           |
| `-w`, `--news`      | Muestra las noticias de Arch (`-ww` incluye las ya leídas).          | `paru -Pw`           |
| `-o`, `--order`     | Muestra el orden de compilación/instalación de los objetivos.       | `paru -Po yay`       |
| `-u`, `--upgrades`  | Lista las actualizaciones disponibles (repos + AUR).                 | `paru -Pu`           |

# -G / --getpkgbuild

Descarga los archivos de compilación sin instalar.

| **Flag**           | **Descripción**                                              | **Ejemplo**          |
| ------------------ | ------------------------------------------------------------ | -------------------- |
| `-p`, `--print`    | Imprime el PKGBUILD por stdout en lugar de clonarlo.        | `paru -Gp yay`       |
| `-c`, `--comments` | Muestra los comentarios del paquete en el AUR.              | `paru -Gc yay`       |
| `-s`, `--ssh`      | Clona vía SSH (útil para mantenedores con commit).         | `paru -Gs yay`       |

# -U / --upgrade y -B / --build

Construyen desde fuentes locales en lugar del AUR remoto.

| **Flag**          | **Descripción**                                                  | **Ejemplo**          |
| ----------------- | ---------------------------------------------------------------- | -------------------- |
| `-U <ruta>`       | Compila e instala el PKGBUILD del directorio/archivo indicado.   | `paru -U ./mipkg`    |
| `-B <dir>`        | Compila e instala desde un directorio con PKGBUILD.             | `paru -B ./mipkg`    |

# -C / --chrootctl y -L / --repoctl

Mantenimiento del entorno de compilación y del repo local (cuando se usa `--chroot`/`--localrepo`).

| **Flag**            | **Descripción**                                              | **Ejemplo**          |
| ------------------- | ------------------------------------------------------------ | -------------------- |
| `-C`, `--chrootctl` | Entra/gestiona el chroot de compilación interactivamente.   | `paru -C`            |
| `-L`, `--repoctl`   | Gestiona el repositorio local de paru.                      | `paru -L`            |

# Configuración

paru se configura en `/etc/paru.conf` (o `~/.config/paru/paru.conf`). Casi toda flag `--opcion`/`--noopcion` tiene su equivalente en el archivo. Opciones de runtime adicionales:

| **Flag**             | **Descripción**                                          | **Ejemplo**                       |
| -------------------- | -------------------------------------------------------- | --------------------------------- |
| `--config <archivo>` | Archivo de configuración de paru alternativo.            | `paru --config ./paru.conf -Syu`  |
| `--save`             | Persiste las opciones de la invocación en la config.     | `paru --bottomup --save`          |
| `--clonedir <ruta>`  | Directorio de clones del AUR.                            | `paru --clonedir ~/.cache/paru -G yay` |

# Notas operativas

- Como pacman, `-Sy` aislado es peligroso; usar `paru` (sin args) o `paru -Syu` para actualizaciones completas.
- **Revisar siempre el PKGBUILD** de paquetes AUR antes de compilar (`--review`); el AUR es contenido no auditado de terceros.
- `paru -Sua` actualiza solo AUR; útil tras un `pacman -Syu` previo.
- Limpieza periódica de clones huérfanos: `paru -Sc` o `paru -c`.
- Toda flag de pacman no listada aquí funciona idéntica al delegarse en él.
