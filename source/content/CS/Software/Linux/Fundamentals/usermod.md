`usermod` modifica una cuenta de usuario ya existente: identidad (UID, login, GECOS), home, shell, pertenencia a grupos, bloqueo, aging y rangos de IDs subordinados. Opera sobre `/etc/passwd`, `/etc/shadow`, `/etc/group`, `/etc/gshadow`, `/etc/subuid` y `/etc/subgid`, aplicando los mismos campos que [[useradd]] pero sobre una cuenta ya creada. Requiere root. Cambiar UID, login o home mientras el usuario tiene procesos en ejecución produce estados inconsistentes: `usermod` lo detecta en Linux y aborta.

```
usermod [opciones] LOGIN
```

> Para añadir grupos secundarios usa **siempre** `-aG`: `-G` por sí solo **reemplaza** toda la lista de grupos secundarios, sacando al usuario de los que no listes.

---

## Identidad

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` / `--login NEW_LOGIN` | Cambia el nombre de login. No renombra el home ni el mail spool. | `usermod -l juan_nuevo juan` |
| `-u` / `--uid UID` | Cambia el UID. Reasigna la propiedad del buzón y de los ficheros dentro del home. No comprueba `UID_MIN`/`UID_MAX`. | `usermod -u 1500 juan` |
| `-o` / `--non-unique` | Permite asignar un UID ya usado. Solo válido junto a `-u`. | `usermod -o -u 1000 juan2` |
| `-c` / `--comment COMMENT` | Cambia el campo GECOS (nombre o descripción). Equivalente no interactivo de `chfn`. | `usermod -c "Juan Pérez" juan` |
| `-b` / `--badname` | Permite nombres de login no conformes al estándar. | `usermod -b -l 'Juan.Perez' juan` |
| `-h` / `--help` | Muestra la ayuda y sale. | `usermod -h` |

> Con `-u` y `-g`, la propiedad de los ficheros **fuera** del home hay que corregirla a mano (`find / -uid VIEJO -exec chown ...`). Tampoco se reasigna dentro del home si el propietario del propio directorio home no coincide con el UID viejo ni con el nuevo: es una salvaguarda para homes especiales como `/`.

---

## Home y shell

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-d` / `--home HOME_DIR` | Define el nuevo directorio home. Por sí solo no mueve ni crea nada. | `usermod -d /srv/juan juan` |
| `-m` / `--move-home` | Mueve el contenido del home anterior al nuevo, creándolo si no existe. Solo válido con `-d`. Intenta adaptar propietario, modos, ACLs y xattrs. | `usermod -m -d /srv/juan juan` |
| `-s` / `--shell SHELL` | Cambia el shell de login. Una cadena vacía vacía el campo en `/etc/passwd` y aplica el shell por defecto del sistema. | `usermod -s /bin/zsh juan` |

> Si el home actual no existe, `-m` **no** crea el nuevo. Comprobar con `getent passwd juan` antes de encadenar cambios.

---

## Grupos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-g` / `--gid GROUP` | Cambia el grupo primario (GID o nombre). El grupo debe existir. Reasigna el grupo de los ficheros del home propiedad del grupo primario anterior. | `usermod -g developers juan` |
| `-G` / `--groups LISTA` | **Reemplaza** la lista completa de grupos suplementarios. Separados por comas, sin espacios. Todos deben existir. | `usermod -G wheel,docker juan` |
| `-a` / `--append` | Junto a `-G`, **añade** sin quitar los grupos actuales. Solo válido con `-G`. | `usermod -aG docker juan` |
| `-r` / `--remove` | Junto a `-G`, **elimina** al usuario de los grupos listados sin tocar el resto. Solo válido con `-G`. | `usermod -rG docker juan` |

> Alternativas equivalentes a `-rG` para sacar de un grupo: `gpasswd -d juan docker` o `deluser juan docker` (Debian).

---

## Bloqueo y aging

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-L` / `--lock` | Bloquea la contraseña anteponiendo `!` al hash en `/etc/shadow`. Incompatible con `-p` y `-U`. | `usermod -L juan` |
| `-U` / `--unlock` | Desbloquea la contraseña quitando el `!`. Incompatible con `-p` y `-L`. | `usermod -U juan` |
| `-e` / `--expiredate EXPIRE_DATE` | Fecha de deshabilitación de la cuenta: `YYYY-MM-DD` o días desde 1970-01-01, en UTC. `-1` o cadena vacía elimina la caducidad. Crea la entrada en `/etc/shadow` si no existe. | `usermod -e 2026-12-31 juan` |
| `-f` / `--inactive INACTIVE` | Días de gracia tras caducar la contraseña durante los que aún se puede entrar cambiándola. `0` inactiva de inmediato; `-1` vacía el campo. | `usermod -f 7 juan` |
| `-p` / `--password PASSWORD` | Establece el hash de contraseña **ya cifrado** (`crypt(3)`). Escribe en el `/etc/shadow` local, que puede no ser la base que usa PAM. | `usermod -p "$(openssl passwd -6 'S3cr3t')" juan` |

> `-L` solo bloquea la **contraseña**: no impide el login por clave SSH ni cierra sesiones activas. Para deshabilitar el acceso completo, combinar con `-s /usr/sbin/nologin` y `-e 1`. Al desbloquear, recordar restaurar también la fecha de caducidad (`-e ''` o el valor de `EXPIRE`).

> `-p` deja el hash visible en `ps` para cualquier usuario. En scripts, preferir `chpasswd`.

---

## IDs subordinados (contenedores rootless)

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v` / `--add-subuids FIRST-LAST` | Añade un rango de UIDs subordinados a `/etc/subuid`. Repetible. No comprueba `SUB_UID_MIN`/`SUB_UID_MAX`/`SUB_UID_COUNT`. | `usermod -v 100000-165535 juan` |
| `-V` / `--del-subuids FIRST-LAST` | Elimina un rango de UIDs subordinados. Repetible. | `usermod -V 100000-165535 juan` |
| `-w` / `--add-subgids FIRST-LAST` | Añade un rango de GIDs subordinados a `/etc/subgid`. Repetible. | `usermod -w 100000-165535 juan` |
| `-W` / `--del-subgids FIRST-LAST` | Elimina un rango de GIDs subordinados. Repetible. | `usermod -W 100000-165535 juan` |
| `-S` / `--add-subids` | Añade rangos de UID y GID subordinados eligiéndolos automáticamente según los defectos de `/etc/login.defs`. | `usermod -S juan` |

> Cuando se combinan `--del-subuids` y `--add-subuids` (o sus equivalentes de GID), **primero** se eliminan todos los rangos indicados y después se añaden los nuevos. Tras modificar estos rangos hay que reiniciar el runtime rootless (`podman system migrate`).

---

## SELinux

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-Z` / `--selinux-user SEUSER` | Usuario SELinux mapeado al login. La cadena vacía (`""`) elimina el mapeo existente. | `usermod -Z staff_u juan` |
| `--selinux-range SERANGE` | Rango MLS/MCS de SELinux. Solo válido junto a `-Z`. | `usermod -Z user_u --selinux-range s0 juan` |

> Solo disponibles en builds de shadow-utils compiladas con soporte SELinux; en Arch o Debian estándar `usermod` las rechaza. El mapeo no se guarda en la base de shadow, sino vía `semanage(8)`.

---

## Raíz alternativa

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-R` / `--root CHROOT_DIR` | Aplica los cambios haciendo chroot en `CHROOT_DIR` y usando sus ficheros de configuración. Solo rutas absolutas. Sin soporte SELinux. | `usermod -R /mnt/sysroot -s /bin/bash juan` |
| `-P` / `--prefix PREFIX_DIR` | Aplica los cambios bajo `PREFIX_DIR` **sin** chroot. Pensado para preparar targets de cross-compilación. No verifica usuarios NIS/LDAP; PAM usa los ficheros del host. | `usermod -P /build/rootfs -s /bin/bash juan` |

---

## Ficheros afectados

| Fichero | Contenido |
|---------|-----------|
| `/etc/passwd` | Login, UID, GID primario, GECOS, home, shell. |
| `/etc/shadow` | Hash de contraseña y campos de aging. |
| `/etc/group` | Grupos y su lista de miembros suplementarios. |
| `/etc/gshadow` | Administradores y contraseñas de grupo. |
| `/etc/subuid`, `/etc/subgid` | Rangos de UID/GID subordinados. |
| `/etc/login.defs` | Defectos globales (`MAIL_DIR`, `LASTLOG_UID_MAX`, `SUB_*`...). |

---

## Limitaciones documentadas

- El usuario no debe estar ejecutando procesos al cambiar UID, login o home. En Linux `usermod` lo comprueba; en otros sistemas solo consulta `utmp`.
- Los `crontab` y trabajos `at` del usuario hay que reasignarlos manualmente.
- Los cambios que impliquen NIS deben hacerse en el servidor NIS.
- La propiedad de ficheros fuera del home no se reasigna con `-u` ni con `-g`.

---

## Casos de uso comunes

```bash
# Añadir a grupos sin sacarlo de los actuales
usermod -aG wheel,docker juan

# Sacarlo solo de un grupo concreto
usermod -rG docker juan

# Cambiar el grupo primario
usermod -g developers juan

# Renombrar login y mover el home en una operación
usermod -l juan_nuevo -d /home/juan_nuevo -m juan

# Cambiar el shell / deshabilitar login interactivo
usermod -s /bin/zsh juan
usermod -s /usr/sbin/nologin svc

# Bloquear una cuenta por completo (contraseña + shell + caducidad)
usermod -L -e 1 -s /usr/sbin/nologin juan

# Desbloquear y quitar la caducidad
usermod -U -e '' juan

# Cambiar UID y reasignar propiedad fuera del home
usermod -u 1500 juan
find / -xdev -uid 1000 -exec chown -h 1500 {} +

# Habilitar contenedores rootless para una cuenta existente
usermod -S juan
usermod -v 200000-265535 -w 200000-265535 juan

# Aplicar cambios sobre un rootfs montado
usermod -R /mnt/sysroot -aG wheel juan
```

---

## Verificación posterior

```bash
getent passwd juan          # UID, GID, home y shell efectivos
id juan                     # grupos primario y suplementarios
getent shadow juan          # estado de bloqueo (! inicial) y aging
passwd -S juan              # resumen: L (locked), P (usable), NP (sin contraseña)
chage -l juan               # política de caducidad
grep '^juan:' /etc/subuid /etc/subgid
```

---

Relacionado: [[useradd]], [[usuarios-y-grupos]], [[chage]], [[sudo]], [[nologin]], [[Hardening]].
