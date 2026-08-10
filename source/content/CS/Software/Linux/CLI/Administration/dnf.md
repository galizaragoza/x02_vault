`dnf` (Dandified YUM) es el gestor de paquetes por defecto en las distribuciones de la familia Red Hat (Fedora, RHEL, CentOS Stream, Rocky, AlmaLinux). Resuelve dependencias con el solver `libsolv`, opera sobre repositorios `.repo` y paquetes RPM, y expone su funcionalidad mediante subcomandos. Muchos subcomandos avanzados (`config-manager`, `download`, `builddep`, `versionlock`, `copr`, `repoquery`, `reposync`, etc.) se entregan como plugins (`dnf-plugins-core`). Esta guía cubre las opciones globales y los subcomandos agrupados por función.

> Sintaxis general: `dnf [opciones-globales] <subcomando> [argumentos]`

# Opciones globales

Aplicables a (casi) cualquier subcomando.

| **Opción** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-h, --help` | Muestra ayuda del programa o del subcomando. | `dnf install --help` |
| `--version` | Muestra la versión de dnf y las versiones de sus componentes. | `dnf --version` |
| `-v, --verbose` | Salida detallada (debug). | `dnf -v install httpd` |
| `-q, --quiet` | Modo silencioso, solo errores. | `dnf -q list installed` |
| `-y, --assumeyes` | Responde "sí" automáticamente a todas las preguntas. | `dnf -y upgrade` |
| `--assumeno` | Responde "no" automáticamente a todas las preguntas. | `dnf --assumeno install nano` |
| `-c, --config=` | Usa un archivo de configuración alternativo. | `dnf -c /etc/dnf/dnf.conf install vim` |
| `--installroot=` | Define un directorio raíz alternativo para la instalación. | `dnf --installroot=/mnt/sys install bash` |
| `--releasever=` | Sobrescribe la versión de release usada en las rutas de repos. | `dnf --releasever=39 distro-sync` |
| `--setopt=` | Sobrescribe una opción de configuración (global o por repo). | `dnf --setopt=install_weak_deps=False install httpd` |
| `--repo, --repoid=` | Restringe la operación a los repos indicados (deshabilita el resto). | `dnf --repo=updates list available` |
| `--enablerepo=` | Habilita repos deshabilitados (acepta glob). | `dnf --enablerepo=epel install neovim` |
| `--disablerepo=` | Deshabilita repos (acepta glob). | `dnf --disablerepo=* --enablerepo=base install gcc` |
| `--disable-plugin=` | Deshabilita plugins concretos por nombre. | `dnf --disable-plugin=copr upgrade` |
| `--enable-plugin=` | Habilita plugins concretos por nombre. | `dnf --enable-plugin=versionlock list` |
| `--noplugins` | Deshabilita todos los plugins en esta ejecución. | `dnf --noplugins makecache` |
| `-x, --exclude=` | Excluye paquetes por nombre o glob de la transacción. | `dnf install '*' -x kernel\*` |
| `--disableexcludes=` | Ignora las exclusiones definidas en la config (`all`, `main` o repoid). | `dnf --disableexcludes=all install httpd` |
| `-C, --cacheonly` | Opera solo desde la caché local, sin contactar repos. | `dnf -C list installed` |
| `--refresh` | Fuerza la actualización de los metadatos antes de la operación. | `dnf --refresh upgrade` |
| `--color=` | Controla el color de salida (`always`/`never`/`auto`). | `dnf --color=never list` |
| `-b, --best` | Intenta siempre la mejor versión disponible aunque rompa. | `dnf -b upgrade` |
| `--nobest` | Permite instalar versiones no óptimas para resolver. | `dnf --nobest upgrade` |
| `--allowerasing` | Permite eliminar paquetes instalados para resolver dependencias. | `dnf --allowerasing install paquete` |
| `--skip-broken` | Omite paquetes con dependencias irresolubles. | `dnf --skip-broken upgrade` |
| `--skip-unavailable` | Omite paquetes no disponibles en los repos. | `dnf --skip-unavailable install a b c` |
| `--nodocs` | No instala la documentación de los paquetes. | `dnf --nodocs install httpd` |
| `--noautoremove` | No elimina automáticamente dependencias huérfanas. | `dnf --noautoremove remove httpd` |
| `--downloadonly` | Descarga los paquetes sin instalarlos. | `dnf --downloadonly install kernel` |
| `--destdir, --downloaddir=` | Directorio destino al usar `--downloadonly` o `download`. | `dnf install --downloadonly --downloaddir=/tmp/rpms vim` |
| `--forcearch=` | Fuerza una arquitectura distinta a la del sistema. | `dnf --forcearch=aarch64 download httpd` |
| `--comment=` | Añade un comentario a la entrada del historial de la transacción. | `dnf --comment="parche CVE" upgrade` |
| `--obsoletes` | Habilita el procesamiento de obsoletos en `upgrade`. | `dnf --obsoletes upgrade` |

# Filtros de seguridad y advisories

Modificadores que restringen `upgrade`, `check-update`, `update-minimal` e `info` a actualizaciones que cumplen un criterio de errata.

| **Opción** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `--security` | Solo actualizaciones marcadas como de seguridad. | `dnf upgrade --security` |
| `--bugfix` | Solo actualizaciones de corrección de bugs. | `dnf upgrade --bugfix` |
| `--enhancement` | Solo actualizaciones de mejora. | `dnf upgrade --enhancement` |
| `--newpackage` | Solo advisories que introducen paquetes nuevos. | `dnf upgrade --newpackage` |
| `--advisory, --advisories=` | Limita a IDs de advisory concretos. | `dnf upgrade --advisory=FEDORA-2024-abc123` |
| `--bz, --bzs=` | Limita a advisories que corrigen un Bugzilla. | `dnf upgrade --bz=1801420` |
| `--cve, --cves=` | Limita a advisories que corrigen un CVE. | `dnf upgrade --cve=CVE-2024-1234` |
| `--sec-severity, --secseverity=` | Filtra por severidad (`Critical`/`Important`/`Moderate`/`Low`). | `dnf upgrade --sec-severity=Critical` |

# Subcomandos: instalación y eliminación

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `install` | Instala uno o más paquetes y sus dependencias. | `dnf install httpd vim` |
| `reinstall` | Reinstala un paquete ya presente. | `dnf reinstall bash` |
| `remove, erase` | Elimina paquetes y las dependencias huérfanas resultantes. | `dnf remove httpd` |
| `autoremove` | Elimina dependencias que ya no requiere ningún paquete. | `dnf autoremove` |
| `swap` | Elimina un paquete e instala otro en una sola transacción. | `dnf swap nano vim` |
| `mark` | Cambia la razón de instalación (`user`/`dependency`/`group`). | `dnf mark user httpd` |

# Subcomandos: actualización

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `upgrade, update` | Actualiza todos los paquetes o los indicados. | `dnf upgrade` |
| `upgrade-minimal, update-minimal` | Actualiza solo a la versión mínima que corrige bugs/seguridad. | `dnf upgrade-minimal --security` |
| `distro-sync` | Sincroniza los paquetes a las versiones de los repos (sube o baja). | `dnf distro-sync` |
| `downgrade` | Instala la versión anterior disponible de un paquete. | `dnf downgrade httpd` |
| `check-update` | Lista actualizaciones disponibles sin aplicarlas (exit code 100 si hay). | `dnf check-update --security` |
| `offline-upgrade` | Descarga ahora y aplica la actualización en el siguiente arranque. | `dnf offline-upgrade download` |

# Subcomandos: consulta de información

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `search` | Busca paquetes por nombre/resumen. `search all` amplía a descripción/url. | `dnf search "web server"` |
| `info` | Muestra metadatos detallados de un paquete. | `dnf info httpd` |
| `list` | Lista paquetes (`--installed`/`--available`/`--upgrades`/`--all`). | `dnf list --installed` |
| `provides, whatprovides` | Indica qué paquete provee un archivo o capacidad. | `dnf provides /usr/bin/htpasswd` |
| `repoquery` | Consulta avanzada de la base de repos (plugin). | `dnf repoquery --requires httpd` |
| `repolist` | Lista los repositorios configurados (`--enabled`/`--disabled`/`--all`). | `dnf repolist --all` |
| `repoinfo` | Información detallada de los repositorios. | `dnf repoinfo updates` |

# Subcomandos: grupos y módulos

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `group list` | Lista los grupos de paquetes disponibles. | `dnf group list` |
| `group info` | Detalla los paquetes de un grupo. | `dnf group info "Development Tools"` |
| `group install` | Instala todos los paquetes de un grupo. | `dnf group install "Development Tools"` |
| `group remove` | Elimina un grupo. | `dnf group remove "Development Tools"` |
| `module list` | Lista los módulos (AppStream) y sus streams/perfiles. | `dnf module list nodejs` |
| `module enable` | Habilita un stream de módulo concreto. | `dnf module enable nodejs:20` |
| `module install` | Instala un perfil de módulo. | `dnf module install nodejs:20/default` |
| `module reset` | Restablece el estado del módulo (ningún stream habilitado). | `dnf module reset nodejs` |
| `module disable` | Deshabilita un módulo por completo. | `dnf module disable nodejs` |

# Subcomandos: historial de transacciones

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `history` | Lista las transacciones realizadas. | `dnf history` |
| `history info` | Detalla una transacción concreta por ID. | `dnf history info 42` |
| `history undo` | Revierte una transacción concreta. | `dnf history undo 42` |
| `history redo` | Repite una transacción concreta. | `dnf history redo 42` |
| `history rollback` | Revierte el sistema al estado posterior a una transacción. | `dnf history rollback 40` |
| `history userinstalled` | Lista paquetes instalados explícitamente por el usuario. | `dnf history userinstalled` |

# Subcomandos: caché y mantenimiento

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `makecache` | Descarga y construye la caché de metadatos. | `dnf makecache` |
| `makecache --timer` | Variante usada por el timer de systemd (respeta el intervalo). | `dnf makecache --timer` |
| `clean all` | Elimina toda la caché (metadatos, paquetes, dbcache). | `dnf clean all` |
| `clean packages` | Elimina solo los paquetes RPM cacheados. | `dnf clean packages` |
| `clean metadata` | Elimina solo los metadatos de repos cacheados. | `dnf clean metadata` |
| `clean expire-cache` | Marca los metadatos como caducados (se refrescan al usarlos). | `dnf clean expire-cache` |
| `check` | Verifica la consistencia de la base de datos de paquetes. | `dnf check --dependencies` |
| `leaves` | Lista paquetes hoja (no requeridos por otros; plugin). | `dnf leaves` |
| `needs-restarting` | Lista procesos/servicios que requieren reinicio (plugin). | `dnf needs-restarting -r` |

# Subcomandos: descarga y construcción (plugins)

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `download` | Descarga el RPM sin instalarlo. | `dnf download httpd` |
| `download --source` | Descarga el paquete fuente (SRPM). | `dnf download --source httpd` |
| `download --resolve` | Descarga el paquete junto a sus dependencias. | `dnf download --resolve httpd` |
| `builddep` | Instala las dependencias de compilación de un paquete o `.spec`. | `dnf builddep httpd.spec` |
| `reposync` | Sincroniza el contenido de un repo a un directorio local. | `dnf reposync --repoid=base -p /mirror` |
| `repoclosure` | Detecta dependencias rotas dentro de un conjunto de repos. | `dnf repoclosure --repo=base` |
| `repodiff` | Compara el contenido de dos repos. | `dnf repodiff --repo-old=old --repo-new=new` |
| `repomanage` | Gestiona RPMs antiguos/nuevos en un árbol de repo. | `dnf repomanage --old /mirror` |

# Subcomandos: repositorios y versionado (plugins)

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `config-manager --add-repo` | Añade un repo desde una URL o archivo `.repo`. | `dnf config-manager --add-repo https://example.com/x.repo` |
| `config-manager --set-enabled` | Habilita un repo de forma persistente. | `dnf config-manager --set-enabled epel` |
| `config-manager --set-disabled` | Deshabilita un repo de forma persistente. | `dnf config-manager --set-disabled testing` |
| `config-manager --setopt` | Fija una opción de config de forma persistente. | `dnf config-manager --setopt=epel.priority=10 --save` |
| `copr enable` | Habilita un repositorio COPR. | `dnf copr enable user/proyecto` |
| `versionlock add` | Bloquea un paquete en su versión actual. | `dnf versionlock add httpd` |
| `versionlock delete` | Elimina un bloqueo de versión. | `dnf versionlock delete httpd` |
| `versionlock list` | Lista los bloqueos activos. | `dnf versionlock list` |

# Subcomando interactivo

| **Subcomando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `shell` | Abre una shell interactiva de dnf para encadenar operaciones en una transacción. | `dnf shell` |
| `shell <archivo>` | Ejecuta un archivo de comandos dnf por lotes. | `dnf shell transacciones.txt` |

## Notas

- En **dnf5** (Fedora 41+) varios alias y formatos de salida cambian; `dnf5 --help <subcomando>` muestra las opciones exactas de la versión instalada.
- El código de salida `100` de `check-update` indica que hay actualizaciones pendientes (útil en scripts).
- Relacionado: [[apt]], [[apt-get]], [[pacman]], [[paru]].
