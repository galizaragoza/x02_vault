El conjunto `useradd`, `userdel`, `groupadd` y `groupdel` forma la capa de bajo nivel para la gestión de cuentas de usuario y grupos en sistemas Linux/Unix. Operan directamente sobre `/etc/passwd`, `/etc/shadow`, `/etc/group` y `/etc/gshadow`, aplicando los valores por defecto definidos en `/etc/login.defs` y `/etc/default/useradd`. A diferencia de los frontends interactivos (`adduser`, `addgroup`), son comandos no interactivos pensados para scripting y automatización. Requieren privilegios de root.

```
useradd  [opciones] LOGIN
userdel  [opciones] LOGIN
groupadd [opciones] GRUPO
groupdel [opciones] GRUPO
```

> Ficheros implicados: `/etc/passwd` (cuentas), `/etc/shadow` (hashes y aging), `/etc/group` (grupos), `/etc/gshadow` (admins/contraseñas de grupo), `/etc/login.defs` (políticas globales), `/etc/default/useradd` (defaults de useradd), `/etc/skel` (plantilla de home).

---

## useradd — creación de usuarios

### Identidad y home

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-m` / `--create-home` | Crea el directorio home y copia `/etc/skel`. | `useradd -m juan` |
| `-M` / `--no-create-home` | Fuerza no crear el home aunque `CREATE_HOME=yes`. | `useradd -M svc` |
| `-d` / `--home-dir DIR` | Define el path del home. | `useradd -m -d /srv/juan juan` |
| `-b` / `--base-dir DIR` | Directorio base donde se crea el home (`BASE_DIR/LOGIN`). | `useradd -m -b /opt/users juan` |
| `-k` / `--skel DIR` | Directorio skeleton alternativo (requiere `-m`). | `useradd -m -k /etc/skel_dev juan` |
| `-u` / `--uid UID` | Asigna un UID concreto. | `useradd -u 1500 juan` |
| `-o` / `--non-unique` | Permite UID duplicado (con `-u`). | `useradd -o -u 0 backdoor` |
| `-c` / `--comment TEXTO` | Campo GECOS (nombre completo, descripción). | `useradd -c "Juan Pérez" juan` |
| `-s` / `--shell SHELL` | Shell de login. | `useradd -s /bin/bash juan` |

### Grupos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-g` / `--gid GRUPO` | Grupo primario (GID o nombre). | `useradd -g developers juan` |
| `-G` / `--groups LISTA` | Grupos secundarios separados por comas. | `useradd -G sudo,docker juan` |
| `-N` / `--no-user-group` | No crea grupo homónimo; usa el grupo por defecto. | `useradd -N juan` |
| `-U` / `--user-group` | Crea un grupo con el mismo nombre que el usuario (UPG). | `useradd -U juan` |

### Contraseña y aging

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-p` / `--password CRYPT` | Hash de contraseña ya cifrado (no texto plano). | `useradd -p "$(openssl passwd -6 's3cr')" juan` |
| `-e` / `--expiredate FECHA` | Fecha de expiración de la cuenta (YYYY-MM-DD). | `useradd -e 2026-12-31 temp` |
| `-f` / `--inactive DÍAS` | Días tras caducar la contraseña antes de deshabilitar. `-1` desactiva. | `useradd -f 7 juan` |

### Cuentas de sistema y SELinux

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r` / `--system` | Crea cuenta de sistema (UID en rango de sistema, sin aging). | `useradd -r -s /usr/sbin/nologin svc` |
| `-l` / `--no-log-init` | No añade al usuario a `lastlog`/`faillog`. | `useradd -l -u 900000 bot` |
| `-Z` / `--selinux-user SEUSER` | Mapea a un usuario SELinux. | `useradd -Z staff_u juan` |
| `--selinux-range RANGO` | Rango MLS/MCS SELinux. | `useradd -Z user_u --selinux-range s0 juan` |

### Defaults, raíz alternativa y ayuda

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-D` / `--defaults` | Sin más args muestra los defaults; con flags los modifica en `/etc/default/useradd`. | `useradd -D` |
| `-D -b DIR` | Cambia el base-dir por defecto. | `useradd -D -b /home` |
| `-D -e / -f / -g / -s` | Cambia expiredate/inactive/grupo/shell por defecto. | `useradd -D -s /bin/bash` |
| `-K` / `--key CLAVE=VAL` | Sobreescribe un valor de `/etc/login.defs`. | `useradd -K UID_MIN=2000 juan` |
| `-R` / `--root DIR` | Aplica los cambios en un árbol chroot. | `useradd -R /mnt/sys juan` |
| `-P` / `--prefix DIR` | Prefijo de ficheros de config (no chroot). | `useradd -P /mnt/etc juan` |
| `--btrfs-subvolume-home` | Crea el home como subvolumen Btrfs (con `-m`). | `useradd -m --btrfs-subvolume-home juan` |
| `-h` / `--help` | Ayuda. | `useradd -h` |

---

## userdel — eliminación de usuarios

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r` / `--remove` | Elimina home, mailbox y ficheros del usuario. | `userdel -r juan` |
| `-f` / `--force` | Fuerza el borrado aunque esté logueado o el home no le pertenezca; combinado con `-r` borra el home igualmente. | `userdel -f -r juan` |
| `-Z` / `--selinux-user` | Elimina los mapeos SELinux del usuario. | `userdel -Z juan` |
| `-R` / `--root DIR` | Opera sobre un chroot. | `userdel -R /mnt/sys juan` |
| `-P` / `--prefix DIR` | Prefijo de ficheros de config. | `userdel -P /mnt/etc juan` |
| `-h` / `--help` | Ayuda. | `userdel -h` |

> `userdel` no elimina ficheros del usuario fuera del home (p. ej. en `/tmp`, `/var`). Busca con `find / -uid <UID>` antes de borrar la cuenta.

---

## groupadd — creación de grupos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-g` / `--gid GID` | Asigna un GID concreto. | `groupadd -g 1500 developers` |
| `-o` / `--non-unique` | Permite GID duplicado (con `-g`). | `groupadd -o -g 0 rootlike` |
| `-r` / `--system` | Crea grupo de sistema (GID en rango de sistema). | `groupadd -r docker` |
| `-f` / `--force` | Si el grupo existe sale con éxito; con `-g` ocupado elige otro GID. | `groupadd -f developers` |
| `-p` / `--password CRYPT` | Hash de contraseña del grupo. | `groupadd -p "$HASH" finanzas` |
| `-K` / `--key CLAVE=VAL` | Sobreescribe valores de `/etc/login.defs` (p. ej. `GID_MIN`). | `groupadd -K GID_MIN=5000 g1` |
| `-R` / `--root DIR` | Opera sobre un chroot. | `groupadd -R /mnt/sys grp` |
| `-P` / `--prefix DIR` | Prefijo de ficheros de config. | `groupadd -P /mnt/etc grp` |
| `-h` / `--help` | Ayuda. | `groupadd -h` |

---

## groupdel — eliminación de grupos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-f` / `--force` | Elimina el grupo aunque sea el grupo primario de algún usuario. | `groupdel -f developers` |
| `-R` / `--root DIR` | Opera sobre un chroot. | `groupdel -R /mnt/sys grp` |
| `-P` / `--prefix DIR` | Prefijo de ficheros de config. | `groupdel -P /mnt/etc grp` |
| `-h` / `--help` | Ayuda. | `groupdel -h` |

> No se puede borrar un grupo que sea el grupo primario de un usuario sin `-f`; reasigna el grupo primario con `usermod -g` antes.

---

## Casos de uso comunes

```bash
# Usuario interactivo completo (home + bash + grupos secundarios)
useradd -m -s /bin/bash -G sudo,docker -c "Juan Pérez" juan
passwd juan

# Cuenta de servicio sin login ni home
useradd -r -s /usr/sbin/nologin -M svc_app

# Usuario con expiración y home en ruta no estándar
useradd -m -d /srv/temp -e 2026-12-31 -s /bin/bash temp

# Crear grupo de sistema y añadir usuario existente
groupadd -r appgrp
usermod -aG appgrp juan

# Borrar usuario eliminando todos sus ficheros del home
userdel -r juan

# Localizar ficheros huérfanos de un UID borrado
find / -uid 1500 2>/dev/null

# Borrar grupo forzando aunque sea primario de alguien
groupdel -f obsoleto

# Ver/ajustar defaults de useradd
useradd -D
useradd -D -s /bin/bash -b /home
```

Relacionado: [[usermod]], [[chage]], [[sudo]], [[Capabilities]].
