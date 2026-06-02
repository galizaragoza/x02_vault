sudo (superuser do) ejecuta un comando con los privilegios de otro usuario (root por defecto), según las reglas definidas en `/etc/sudoers` y `/etc/sudoers.d/`. Registra cada invocación (auditoría) y cachea la autenticación durante un tiempo (timestamp), evitando pedir la contraseña en cada comando. Es el mecanismo estándar de escalada de privilegios controlada en Linux, preferible a iniciar sesión como root.

```
sudo [opciones] [-u usuario] comando
```

---

## Ejecución de comandos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-u <usuario>` / `--user` | Ejecuta el comando como ese usuario (no root). | `sudo -u www-data whoami` |
| `-g <grupo>` / `--group` | Ejecuta con el grupo indicado. | `sudo -g docker id` |
| `-l` / `--list` | Lista los comandos permitidos al usuario actual. | `sudo -l` |
| `-k` / `--reset-timestamp` | Invalida la caché de credenciales: el próximo `sudo` volverá a pedir contraseña. | `sudo -k` |
| `-v` / `--validate` | Renueva el timestamp sin ejecutar comando (extiende la sesión). | `sudo -v` |
| `-b` / `--background` | Ejecuta el comando en segundo plano. | `sudo -b apt update` |
| `-E` / `--preserve-env` | Conserva las variables de entorno del usuario actual. | `sudo -E ./script.sh` |
| `-n` / `--non-interactive` | No pide contraseña; falla si haría falta (útil en scripts/cron). | `sudo -n systemctl restart nginx` |
| `-S` / `--stdin` | Lee la contraseña desde stdin. | `echo "$PASS" \| sudo -S apt update` |
| `-p <texto>` / `--prompt` | Personaliza el prompt de contraseña. | `sudo -p "Clave: " id` |

---

## Shells interactivas y cambio de usuario

| Opción / Comando | Descripción | Ejemplo |
|------------------|-------------|---------|
| `-i` / `--login` | Abre una shell de login como el usuario objetivo: carga su entorno (`~/.profile`, `PATH`, `HOME`, etc.) y cambia al `$HOME` destino. | `sudo -i` |
| `-s` / `--shell` | Abre la shell indicada en `$SHELL` con privilegios, **sin** ser shell de login (conserva en gran parte el entorno actual). | `sudo -s` |
| `sudo su` | Lanza `su` vía sudo: shell de root pero **conservando** el entorno y `$HOME` del usuario original (shell no-login). | `sudo su` |
| `sudo su -` | Lanza `su` con `-` (login): entorno **limpio** de root, ejecuta los scripts de login y cambia a `/root`. Equivale en la práctica a `sudo -i`. | `sudo su -` |
| `sudo su - <usuario>` | Login shell completa como otro usuario. | `sudo su - postgres` |

> **`sudo su` vs `sudo su -`:** el guion (`-` o `-l`) marca la diferencia entre una *login shell* y una *non-login shell*. `sudo su -` reinicia `PATH`, `HOME`, `SHELL` y demás variables como si root iniciara sesión desde cero; `sudo su` hereda el entorno del usuario que invoca. Para tareas administrativas reproducibles, preferir `sudo -i` o `sudo su -`.

---

## Edición segura de configuración

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `sudo visudo` | Edita `/etc/sudoers` validando la sintaxis antes de guardar (evita dejar el sistema sin sudo). | `sudo visudo` |
| `sudo visudo -f <fichero>` | Edita un fichero concreto de `/etc/sudoers.d/`. | `sudo visudo -f /etc/sudoers.d/devops` |
| `sudo visudo -c` | Solo comprueba la sintaxis. | `sudo visudo -c` |

---

## Sintaxis de `/etc/sudoers`

Formato general de una regla: `usuario  host=(usuario_destino:grupo)  [etiquetas:]  comandos`.

```sh
User_Alias ADMINS = user1, user2          ## Define alias ADMINS

ADMINS ALL = NOPASSWD: ALL                ## Los ADMINS pueden ejecutar todos los comandos (segundo ALL) en todos los hosts (primer ALL) sin contraseña

root ALL=(ALL) ALL                        ## root puede ejecutar cualquier comando; (ALL) = puede suplantar a cualquier usuario (sudo -u user1 cmd)

%sudo ALL=(ALL:ALL) ALL                   ## Cualquier miembro del grupo sudo (el % indica grupo) puede ejecutar cualquier comando como cualquier usuario/grupo

ana ALL=(www-data) /usr/bin/systemctl restart nginx   ## ana solo puede reiniciar nginx, y como www-data
```

| Elemento | Significado |
|----------|-------------|
| `ALL` (1º) | Hosts a los que aplica la regla. |
| `(usuario:grupo)` | Identidad que puede suplantar (`sudo -u` / `-g`). |
| `NOPASSWD:` | No solicita contraseña para los comandos siguientes. |
| `PASSWD:` | Fuerza solicitud de contraseña (por defecto). |
| `%grupo` | La regla aplica a un grupo del sistema. |
| `User_Alias` / `Cmnd_Alias` / `Host_Alias` | Definen alias reutilizables de usuarios, comandos y hosts. |

---

## Casos de uso comunes

```bash
# Ejecutar un único comando como root
sudo systemctl restart sshd

# Shell de root con entorno limpio (administración)
sudo -i          # o: sudo su -

# Ejecutar como otro usuario de servicio
sudo -u postgres psql

# Ver qué tengo permitido ejecutar
sudo -l

# Refrescar credenciales antes de un script largo
sudo -v

# Cerrar la sesión cacheada de sudo
sudo -k

# Reabrir el último comando con privilegios
sudo !!
```
