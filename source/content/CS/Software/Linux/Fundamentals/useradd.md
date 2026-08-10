`useradd` crea cuentas de usuario locales combinando los valores de la línea de comandos con los defectos del sistema (`/etc/default/useradd` y `/etc/login.defs`). Escribe en `/etc/passwd`, `/etc/shadow`, `/etc/group` y `/etc/gshadow`, y opcionalmente crea el directorio home copiando el skeleton. Es la utilidad de bajo nivel del paquete *shadow-utils*: no es interactiva y no pregunta por la contraseña, a diferencia del envoltorio Perl `adduser` de Debian. Requiere root.

```
useradd [opciones] LOGIN
useradd -D
useradd -D [opciones]
```

> Recién creada, la cuenta queda **bloqueada** (`!` en el campo de contraseña de `/etc/shadow`) salvo que se use `-p`. Hay que asignar contraseña con `passwd LOGIN` para que sea utilizable.

---

## Identidad y credenciales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-u` / `--uid UID` | UID numérico de la cuenta. Debe ser único (salvo con `-o`) y no negativo. Por defecto, el menor libre ≥ `UID_MIN`. | `useradd -u 1500 juan` |
| `-o` / `--non-unique` | Permite un UID ya existente. Solo válido junto a `-u`. Dos logins comparten permisos y propiedad de ficheros. | `useradd -o -u 1000 juan2` |
| `-c` / `--comment COMMENT` | Campo GECOS: descripción o nombre completo de la cuenta. | `useradd -c "Juan Pérez" juan` |
| `-p` / `--password PASSWORD` | Contraseña **ya cifrada** (formato `crypt(3)`). Sin esta opción la cuenta nace bloqueada. | `useradd -p "$(openssl passwd -6 'S3cr3t')" juan` |
| `--badname` | Omite la validación de nombres de login no conformes al estándar (mayúsculas, `.`, `$` final...). | `useradd --badname 'Juan.Perez'` |
| `-h` / `--help` | Muestra la ayuda y sale. | `useradd -h` |

> `-p` expone el hash en la línea de comandos, visible en `ps` para cualquier usuario del sistema. En scripts, preferir `chpasswd` o `passwd --stdin` alimentado por tubería.

---

## Directorio home y skeleton

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-m` / `--create-home` | Crea el home si no existe y copia en él el contenido del skeleton. | `useradd -m juan` |
| `-M` / `--no-create-home` | No crea el home aunque `CREATE_HOME=yes` en `/etc/login.defs`. Prevalece sobre `-m`. | `useradd -M svc_app` |
| `-d` / `--home-dir HOME_DIR` | Ruta del home. Por defecto `BASE_DIR/LOGIN`. No se crea salvo con `-m` o `CREATE_HOME`. | `useradd -m -d /srv/juan juan` |
| `-b` / `--base-dir BASE_DIR` | Directorio base al que se concatena el login para formar el home, si no se da `-d`. Por defecto `HOME` de `/etc/default/useradd`, o `/home`. | `useradd -m -b /export/home juan` |
| `-k` / `--skel SKEL_DIR` | Skeleton alternativo a `/etc/skel`. Solo válido con `-m`. Copia ACLs y atributos extendidos si es posible. | `useradd -m -k /etc/skel_dev juan` |
| `--btrfs-subvolume-home` | Crea el home como subvolumen Btrfs. Si el padre no es Btrfs, crea un directorio normal y avisa por syslog (`LOG_WARN`). | `useradd -m --btrfs-subvolume-home juan` |

> El directorio padre del home debe existir y tener permisos y contexto SELinux correctos; en caso contrario el home no se crea o queda inaccesible. Si el tipo de filesystem no puede determinarse con `--btrfs-subvolume-home`, `useradd` aborta con código 12.

---

## Grupos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-g` / `--gid GROUP` | Grupo primario, por nombre o GID. El grupo debe existir previamente. | `useradd -g developers juan` |
| `-G` / `--groups LISTA` | Grupos suplementarios separados por comas, sin espacios. Todos deben existir. | `useradd -G wheel,docker,audio juan` |
| `-U` / `--user-group` | Crea un grupo homónimo del usuario y lo asigna como primario (UPG). | `useradd -U juan` |
| `-N` / `--no-user-group` | No crea grupo homónimo; usa el de `-g` o la variable `GROUP` de `/etc/default/useradd`. | `useradd -N -g users juan` |

> El comportamiento por defecto cuando no se indica `-g`, `-N` ni `-U` lo decide `USERGROUPS_ENAB` en `/etc/login.defs`. Con `yes` (habitual en Arch, RHEL, Debian) se crea el grupo homónimo.

---

## Caducidad, aging y defaults de login.defs

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-e` / `--expiredate EXPIRE_DATE` | Fecha de deshabilitación de la cuenta: `YYYY-MM-DD` o días desde 1970-01-01. Se interpreta en UTC. | `useradd -e 2026-12-31 becario` |
| `-f` / `--inactive INACTIVE` | Días de gracia tras caducar la contraseña antes de inactivar la cuenta. `0` la inactiva de inmediato; `-1` vacía el campo (sin aplicar). | `useradd -f 7 juan` |
| `-K` / `--key KEY=VALUE` | Sobrescribe una variable de `/etc/login.defs` solo para esta invocación. Repetible. | `useradd -K UID_MIN=2000 -K UID_MAX=2999 juan` |

> El resto del aging de contraseña (mínimo, máximo, aviso) se gestiona después con [[chage]] o `passwd -n/-x/-w`.

---

## Cuentas de sistema e IDs subordinados

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r` / `--system` | Cuenta de sistema: UID en el rango `SYS_UID_MIN`–`SYS_UID_MAX`, sin información de aging en `/etc/shadow` y sin home aunque `CREATE_HOME=yes`. | `useradd -r -s /usr/sbin/nologin svc_nginx` |
| `-F` / `--add-subids-for-system` | Añade entradas a `/etc/subuid` y `/etc/subgid` también para cuentas creadas con `-r`. | `useradd -r -F svc_podman` |

> `-r` **no** actualiza `/etc/subuid` ni `/etc/subgid` por sí solo: sin `-F` la cuenta no puede lanzar contenedores rootless. Rangos definidos por `SUB_UID_MIN`/`SUB_UID_MAX`/`SUB_UID_COUNT` en `/etc/login.defs`.

---

## Shell y registro de accesos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-s` / `--shell SHELL` | Shell de login. Sin la opción se usa `SHELL` de `/etc/default/useradd`; si tampoco está, el campo queda vacío. | `useradd -s /bin/zsh juan` |
| `-l` / `--no-log-init` | No inicializa las entradas del usuario en las bases `lastlog` y `faillog`. Equivale a `LOG_INIT=no`. | `useradd -l -u 900000 ldap_stub` |

> `-l` evita que se cree un fichero `lastlog` disperso enorme al usar UIDs muy altos (típico con LDAP/AD). No aparece en `useradd --help` en algunas builds, pero sí está documentado e implementado.

---

## SELinux

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-Z` / `--selinux-user SEUSER` | Usuario SELinux mapeado a la cuenta. Sin la opción se usa el mapeo por defecto. | `useradd -Z staff_u juan` |
| `--selinux-range SERANGE` | Rango MLS/MCS de SELinux. Solo válido junto a `-Z`. | `useradd -Z user_u --selinux-range s0 juan` |

> Ambas opciones solo existen en builds de shadow-utils compiladas con soporte SELinux (RHEL, Fedora); en Arch o Debian estándar `useradd` las rechaza. El mapeo no se guarda en la base de shadow, sino vía `semanage(8)`.

---

## Raíz alternativa

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-R` / `--root CHROOT_DIR` | Aplica los cambios haciendo chroot en `CHROOT_DIR` y usando sus ficheros de configuración. Solo rutas absolutas. Sin soporte SELinux. | `useradd -R /mnt/sysroot -m juan` |
| `-P` / `--prefix PREFIX_DIR` | Aplica los cambios bajo `PREFIX_DIR` **sin** chroot. Pensado para preparar targets de cross-compilación. No verifica usuarios NIS/LDAP. | `useradd -P /build/rootfs -m juan` |

---

## Modo `-D`: consultar y cambiar los valores por defecto

Invocado solo con `-D`, imprime los defectos actuales; con `-D` más opciones, los reescribe en `/etc/default/useradd`.

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-D` / `--defaults` | Muestra los valores por defecto de creación de cuentas. | `useradd -D` |
| `-D -b` / `--base-dir BASE_DIR` | Fija la variable `HOME`: prefijo del home de los nuevos usuarios. | `useradd -D -b /export/home` |
| `-D -e` / `--expiredate EXPIRE_DATE` | Fija la variable `EXPIRE`: fecha de caducidad por defecto. | `useradd -D -e 2027-01-01` |
| `-D -f` / `--inactive INACTIVE` | Fija la variable `INACTIVE`: días de inactividad tras caducar la contraseña. | `useradd -D -f 30` |
| `-D -g` / `--gid GROUP` | Fija la variable `GROUP`: grupo primario por defecto. El grupo debe existir. | `useradd -D -g users` |
| `-D -s` / `--shell SHELL` | Fija la variable `SHELL`: shell de login por defecto. | `useradd -D -s /bin/bash` |

> En sistemas donde `/etc/default/useradd` no existe (caso de Arch), `useradd -D` muestra los valores compilados y `useradd -D <opción>` crea el fichero.

---

## Ficheros afectados

| Fichero | Contenido |
|---------|-----------|
| `/etc/passwd` | Login, UID, GID primario, GECOS, home, shell. |
| `/etc/shadow` | Hash de contraseña y campos de aging. |
| `/etc/group` | Grupos y su lista de miembros suplementarios. |
| `/etc/gshadow` | Administradores y contraseñas de grupo. |
| `/etc/subuid`, `/etc/subgid` | Rangos de UID/GID subordinados (contenedores rootless). |
| `/etc/login.defs` | Defectos globales del sistema shadow (`UID_MIN`, `CREATE_HOME`, `USERGROUPS_ENAB`...). |
| `/etc/default/useradd` | Defectos propios de `useradd`, editables con `-D`. |
| `/etc/skel/` | Plantilla copiada al home con `-m`. |

---

## Códigos de salida

| Código | Significado |
|--------|-------------|
| `0` | Éxito. |
| `1` | No se puede actualizar el fichero de contraseñas. |
| `2` | Sintaxis de comando inválida. |
| `3` | Argumento inválido para una opción. |
| `4` | UID ya en uso (y no se dio `-o`). |
| `6` | El grupo especificado no existe. |
| `9` | El nombre de usuario o de grupo ya está en uso. |
| `10` | No se puede actualizar el fichero de grupos. |
| `12` | No se puede crear el directorio home. |
| `14` | No se puede actualizar el mapeo de usuario SELinux. |
| `19` | Nombre de usuario o de grupo inválido. |

---

## Casos de uso comunes

```bash
# Usuario interactivo estándar: home, skeleton, shell y grupo propio
useradd -m -s /bin/bash -U juan
passwd juan

# Usuario con grupos suplementarios de administración y contenedores
useradd -m -s /bin/bash -G wheel,docker juan

# Cuenta de servicio sin login ni home
useradd -r -M -s /usr/sbin/nologin -c "nginx worker" svc_nginx

# Cuenta de servicio para contenedores rootless (necesita -F)
useradd -r -m -F -s /usr/sbin/nologin svc_podman

# Cuenta temporal que caduca en una fecha concreta
useradd -m -s /bin/bash -e 2026-12-31 -f 7 becario

# Usuario con UID y home fuera de los defectos
useradd -m -u 2500 -d /srv/apps/deploy -s /bin/bash deploy

# Alta no interactiva con contraseña ya hasheada (evitar en shell interactiva)
useradd -m -s /bin/bash -p "$(openssl passwd -6 "$PASS")" juan

# Preparar un rootfs montado sin arrancarlo
useradd -R /mnt/sysroot -m -s /bin/bash juan

# Consultar y modificar los defectos del sistema
useradd -D
useradd -D -s /bin/zsh -b /export/home
```

---

## Verificación posterior

```bash
getent passwd juan          # entrada en /etc/passwd (incluye NSS: LDAP, SSSD)
getent shadow juan          # hash y aging (requiere root)
id juan                     # UID, GID primario y grupos suplementarios
chage -l juan               # política de caducidad efectiva
grep '^juan:' /etc/subuid /etc/subgid   # rangos subordinados
```

---

Relacionado: [[usermod]], [[usuarios-y-grupos]], [[chage]], [[sudo]], [[nologin]], [[Hardening]].
