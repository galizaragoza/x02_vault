`usermod` modifica una cuenta de usuario existente: identidad (UID, login, GECOS), home, shell, pertenencia a grupos, bloqueo y aging. Opera sobre `/etc/passwd`, `/etc/shadow` y `/etc/group`, aplicando los mismos campos que `useradd` pero sobre una cuenta ya creada. Requiere root. La mayoría de cambios fallan o son inconsistentes si el usuario tiene procesos en ejecución, salvo `-G`/`-a`.

```
usermod [opciones] LOGIN
```

> Para añadir grupos secundarios usa **siempre** `-aG`: `-G` por sí solo **reemplaza** toda la lista de grupos secundarios, sacando al usuario de los que no listes.

---

## Identidad

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` / `--login NUEVO` | Cambia el nombre de login (no renombra el home). | `usermod -l juan_nuevo juan` |
| `-u` / `--uid UID` | Cambia el UID; reasigna la propiedad de ficheros del home. | `usermod -u 1500 juan` |
| `-o` / `--non-unique` | Permite UID duplicado (con `-u`). | `usermod -o -u 0 juan` |
| `-c` / `--comment TEXTO` | Cambia el campo GECOS (nombre/descripción). | `usermod -c "Juan Pérez" juan` |

---

## Home y shell

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-d` / `--home DIR` | Define el nuevo directorio home (no mueve el contenido por sí solo). | `usermod -d /srv/juan juan` |
| `-m` / `--move-home` | Mueve el contenido del home anterior al nuevo (requiere `-d`). | `usermod -m -d /srv/juan juan` |
| `-s` / `--shell SHELL` | Cambia el shell de login. `/usr/sbin/nologin` lo deshabilita. | `usermod -s /bin/zsh juan` |

---

## Grupos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-g` / `--gid GRUPO` | Cambia el grupo primario (GID o nombre). | `usermod -g developers juan` |
| `-G` / `--groups LISTA` | **Reemplaza** la lista completa de grupos secundarios. | `usermod -G sudo,docker juan` |
| `-a` / `--append` | Junto a `-G`, **añade** sin quitar los grupos actuales. | `usermod -aG docker juan` |
| `-r` / `--remove` | (En `-aG`, según versión) elimina al usuario de los grupos dados. | `usermod -rG docker juan` |

> Para sacar a un usuario de un grupo concreto sin tocar el resto, usa `gpasswd -d juan docker` o `deluser juan docker`.

---

## Bloqueo y aging

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-L` / `--lock` | Bloquea la contraseña (antepone `!` al hash en shadow). | `usermod -L juan` |
| `-U` / `--unlock` | Desbloquea la contraseña. | `usermod -U juan` |
| `-e` / `--expiredate FECHA` | Fecha de expiración de la cuenta (YYYY-MM-DD; vacío la quita). | `usermod -e 2026-12-31 juan` |
| `-f` / `--inactive DÍAS` | Días tras caducar la contraseña antes de deshabilitar. `-1` desactiva. | `usermod -f 7 juan` |
| `-p` / `--password CRYPT` | Establece el hash de contraseña (ya cifrado). | `usermod -p "$HASH" juan` |

> `-L` solo bloquea la **contraseña**, no impide login por clave SSH ni cierra sesiones activas. Para deshabilitar acceso completo combina con `-s /usr/sbin/nologin` y/o `-e 1`.

---

## SELinux y raíz alternativa

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-Z` / `--selinux-user SEUSER` | Define el mapeo de usuario SELinux. | `usermod -Z staff_u juan` |
| `--selinux-range RANGO` | Rango MLS/MCS SELinux. | `usermod -Z user_u --selinux-range s0 juan` |
| `-R` / `--root DIR` | Aplica los cambios sobre un árbol chroot. | `usermod -R /mnt/sys -s /bin/bash juan` |
| `-P` / `--prefix DIR` | Prefijo de ficheros de config (no chroot). | `usermod -P /mnt/etc juan` |
| `-h` / `--help` | Ayuda. | `usermod -h` |

---

## Casos de uso comunes

```bash
# Añadir un usuario a grupos sin sacarlo de los actuales
usermod -aG sudo,docker juan

# Cambiar el grupo primario
usermod -g developers juan

# Renombrar login y mover el home en una operación
usermod -l juan_nuevo -d /home/juan_nuevo -m juan

# Cambiar el shell / deshabilitar login interactivo
usermod -s /bin/zsh juan
usermod -s /usr/sbin/nologin svc

# Bloquear una cuenta (contraseña) y darle caducidad pasada
usermod -L -e 1 juan

# Desbloquear
usermod -U juan

# Cambiar UID y reasignar propiedad de su home
usermod -u 1500 juan
```

Relacionado: [[usuarios-y-grupos]], [[chage]], [[sudo]], [[Hardening]].
