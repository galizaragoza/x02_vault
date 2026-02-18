# GUI over SSH
Es posible enrutar entornos gráficos de forma encriptada a través de SSH para obtener una GUI completa con aplicaciones y desktop en remotamente. Esto es clave cuando los requerimientos de OpSec son altos, ya que nos permite manipular el bouncing server que hayamos setupeado en el CP de confianza cómodamente desde nuestra máquina (junto con VPN y TOR, por supuesto). 
## How-to
1. En el servidor remoto, editar `/etc/ssh/sshd_config`
```zsh
AllowTcpForwarding yes 
X11Forwarding yes 
X11DisplayOffset 10 
X11UseLocalhost yes
```
2. En el host, editar `/etc/ssh/ssh_config`
```zsh
ForwardX11 yes
```
3. Reiniciar el servicio
```zsh
sudo systemctl restart sshd
```
> "In this example, SSH connections from _anywhere_ will have the ability to forward X11 traffic. This isn’t ideal. Specify the hostnames or IP ranges that get this privilege to _only_ the hosts that need it to remain consistent with the Principle of Least Privilege."

Esto se puede conseguir con la GUI del CP o con firewall rules en el servidor. Ver [[ufw]]


# Port forwarding

![[ssh-tunnels.png]]


# Recursos
[How To Reuse SSH Connection To Speed Up Remote Login Process Using Multiplexing](https://www.cyberciti.biz/faq/linux-unix-reuse-openssh-connection/)
[SSH in 5 min CyberFlow](https://youtu.be/dkXZYinZQg0)
[A quick and dirty guide to X11 forwarding over SSH](https://some-natalie.dev/blog/ssh-x11-forwarding/)
[A Visual Guide to SSH Tunnels: Local and Remote Port Forwarding](https://iximiuz.com/en/posts/ssh-tunnels/)