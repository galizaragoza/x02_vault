# ssh (cliente)

**`ssh`** es el cliente de login remoto de OpenSSH. Abre una sesión cifrada y autenticada contra un `sshd` remoto para ejecutar una shell interactiva o un comando puntual. Además del acceso, gestiona reenvío de puertos (local, remoto, dinámico/SOCKS), reenvío de X11 y del agente, salto por hosts intermedios (ProxyJump) y multiplexado de conexiones. La sintaxis base es `ssh [opciones] [user@]host [comando]`.

Parte del índice de la suite: [[OpenSSH]].

```
ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-B bind_iface] [-b bind_addr] [-c cipher] [-D [bind:]port]
    [-E log_file] [-e escape] [-F configfile] [-I pkcs11] [-i identity] [-J destino]
    [-L addr] [-l login] [-m mac] [-O ctl_cmd] [-o opt] [-P tag] [-p port] [-Q query]
    [-R addr] [-S ctl_path] [-W host:port] [-w tun] destino [comando [arg...]]
```

---

## Selección de red / protocolo

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-4` | Fuerza solo IPv4. | `ssh -4 user@host` |
| `-6` | Fuerza solo IPv6. | `ssh -6 user@host` |
| `-B bind_interface` | Liga la conexión saliente a una interfaz concreta (por nombre). | `ssh -B eth1 user@host` |
| `-b bind_address` | Liga la conexión saliente a una IP local concreta. | `ssh -b 10.0.0.5 user@host` |
| `-p port` | Puerto del servidor (por defecto 22). | `ssh -p 2222 user@host` |
| `-l login_name` | Usuario de login (alternativa a `user@`). | `ssh -l root 10.10.10.10` |

---

## Autenticación e identidad

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-i identity_file` | Clave privada a usar. Repetible. | `ssh -i ~/.ssh/id_ed25519 user@host` |
| `-I pkcs11` | Librería PKCS#11 para claves en token/smartcard. | `ssh -I /usr/lib/opensc-pkcs11.so user@host` |
| `-A` | Reenvía el agente de autenticación (`ssh-agent`) al remoto. **Riesgo**: root remoto puede usar tus claves. | `ssh -A user@bastion` |
| `-a` | Deshabilita el reenvío del agente (default). | `ssh -a user@host` |
| `-K` | Habilita autenticación y delegación GSSAPI (Kerberos). | `ssh -K user@host.dominio` |
| `-k` | Deshabilita delegación de credenciales GSSAPI. | `ssh -k user@host` |
| `-o PasswordAuthentication=no` | Solo clave, no contraseña (evita colgarse en prompts). | `ssh -i k -o PasswordAuthentication=no root@host` |

---

## Ejecución y terminal

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `ssh host comando` | Ejecuta un comando remoto y sale (one-liner). | `ssh user@10.10.10.10 whoami` |
| `-t` | Fuerza asignación de pseudo-terminal (programas curses, `sudo`, `su`). | `ssh -t user@host 'sudo -i'` |
| `-T` | Deshabilita la pseudo-terminal (túneles, transferencias). | `ssh -T git@github.com` |
| `-N` | No ejecuta comando remoto (solo forwarding). | `ssh -N -L 8080:127.0.0.1:80 user@host` |
| `-n` | Redirige stdin desde `/dev/null` (para `-f`). | `ssh -n -f user@host 'sleep 60'` |
| `-f` | Pasa a segundo plano tras autenticar (implica no leer stdin). | `ssh -fN -L 3389:10.0.0.9:3389 user@host` |
| `-s` | Invoca un subsistema en vez de un comando (p. ej. `sftp`, `netconf`). | `ssh user@host -s sftp` |
| `-e escape_char` | Cambia el carácter de escape (default `~`). `none` lo desactiva. | `ssh -e none user@host` |

---

## Reenvío de puertos (tunneling)

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-L [bind:]lport:host:hport` | **Local forward**: puerto local → destino accesible desde el remoto. | `ssh -L 8080:10.10.10.10:80 user@gateway` |
| `-R [bind:]rport:host:hport` | **Remote forward**: puerto en el remoto → destino accesible desde el cliente (reverse). | `ssh -R 2222:localhost:22 user@vps` |
| `-D [bind:]port` | **Dynamic forward**: proxy SOCKS4/5 sobre el túnel. | `ssh -D 1080 user@vps` → `proxychains ...` |
| `-g` | Permite que hosts remotos usen tus puertos locales reenviados. | `ssh -g -L 8080:target:80 user@host` |
| `-w local_tun[:remote_tun]` | Túnel de dispositivo `tun` (VPN capa 3 punto a punto). | `ssh -w 0:0 root@vpn-peer` |
| `-W host:port` | Reenvía stdin/stdout a `host:port` por el canal seguro (usado como `ProxyCommand`). | `ssh -W 10.0.0.9:22 user@bastion` |
| `-O ctl_cmd` | Controla una conexión maestra: `check`, `forward`, `cancel`, `exit`, `stop`. | `ssh -O cancel -L 8080:t:80 host` |

---

## Salto / proxy / multiplexado

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-J destino` | ProxyJump: encadena uno o más bastiones. Repetible con comas. | `ssh -J user@bastion user@internal` |
| `-o ProxyCommand=...` | Comando externo que provee la conexión (p. ej. `nc`, sin ssh en el bastión). | `ssh -o ProxyCommand="nc -X connect -x proxy:8080 %h %p" user@target` |
| `-M` | Modo maestro para compartir conexión (ControlMaster). Doble `-M` pide confirmación. | `ssh -M -S /tmp/ctl user@host` |
| `-S ctl_path` | Ruta del socket de control para multiplexado. `none` lo desactiva. | `ssh -S /tmp/ctl-%r@%h:%p user@host` |

---

## Reenvío de X11 y GUI

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-X` | Habilita reenvío X11 (aplicaciones gráficas remotas). | `ssh -X user@host xterm` |
| `-Y` | Reenvío X11 **confiable** (sin restricciones de la extensión SECURITY). | `ssh -Y user@host wireshark` |
| `-x` | Deshabilita reenvío X11 (default). | `ssh -x user@host` |

---

## Cifrado y compresión

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-C` | Comprime todos los datos (útil en enlaces lentos). | `ssh -C user@host` |
| `-c cipher_spec` | Algoritmo(s) de cifrado. Lista con `-Q cipher`. | `ssh -c aes256-gcm@openssh.com user@host` |
| `-m mac_spec` | Algoritmo(s) MAC. Lista con `-Q mac`. | `ssh -m hmac-sha2-512 user@host` |

---

## Configuración y opciones

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-F configfile` | Usa un `ssh_config` alternativo. `-F none` ignora config de usuario. | `ssh -F ./ops.cfg user@host` |
| `-o option` | Opción de `ssh_config(5)` en línea. Repetible. | `ssh -o StrictHostKeyChecking=no user@host` |
| `-P tag` | Nombre de tag para seleccionar bloques `Match tag` en la config. | `ssh -P prod user@host` |
| `-G` | Imprime la configuración efectiva (tras `Host`/`Match`) y sale. Útil para depurar. | `ssh -G user@host` |
| `-Q query_option` | Consulta algoritmos soportados: `cipher`, `mac`, `kex`, `key`, `sig`, etc. | `ssh -Q kex` |

---

## Diagnóstico y varios

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-v` | Verbose. Repetible (`-vvv`) para más detalle de handshake. | `ssh -vvv user@host` |
| `-q` | Modo silencioso (suprime warnings). | `ssh -q user@host` |
| `-E log_file` | Escribe los logs de debug a fichero en vez de stderr. | `ssh -vvv -E ssh.log user@host` |
| `-y` | Envía logs vía syslog en vez de stderr. | `ssh -y user@host` |
| `-V` | Muestra la versión y sale. | `ssh -V` |

---

## Opciones `-o` de uso frecuente

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `StrictHostKeyChecking=no` | No pregunta ni bloquea por clave de host desconocida. | `ssh -o StrictHostKeyChecking=no user@host` |
| `UserKnownHostsFile=/dev/null` | Ignora/no persiste `known_hosts` (labs efímeros). | `ssh -o UserKnownHostsFile=/dev/null user@host` |
| `ConnectTimeout=5` | Timeout de conexión en segundos. | `ssh -o ConnectTimeout=5 user@host` |
| `ServerAliveInterval=30` | Keepalive: paquete cada N s para no caer la sesión. | `ssh -o ServerAliveInterval=30 user@host` |
| `HostKeyAlgorithms=+ssh-rsa` | Rehabilita algoritmos legacy en servers antiguos. | `ssh -o HostKeyAlgorithms=+ssh-rsa user@old` |
| `PubkeyAuthentication=no` | Fuerza autenticación por contraseña. | `ssh -o PubkeyAuthentication=no user@host` |
| `ProxyJump=user@bastion` | Equivalente a `-J` como opción. | `ssh -o ProxyJump=user@bastion user@internal` |
| `RemoteCommand=...` | Comando a ejecutar tras conectar. | `ssh -o RemoteCommand="id" user@host` |

---

## Escape sequences (sesión interactiva)

Se teclean tras un `Enter`; carácter de escape por defecto `~`:

| Secuencia | Acción |
| --- | --- |
| `~.` | Cierra la conexión forzosamente (sesión colgada). |
| `~^Z` | Suspende ssh (background). |
| `~#` | Lista las conexiones reenviadas. |
| `~C` | Abre línea de comandos (añadir/cancelar `-L`/`-R`/`-D` en caliente). |
| `~R` | Solicita re-key de la conexión. |
| `~?` | Muestra la ayuda de secuencias de escape. |

---

## Casos prácticos

```bash
# Pivoting: SOCKS + proxychains
ssh -fN -D 1080 user@vps
proxychains -q nmap -sT -Pn 10.10.10.0/24

# Doble salto encadenado
ssh -J user@bastion1,user@bastion2 user@interno

# Reverse shell persistente vía reverse forward
ssh -fN -R 2222:localhost:22 user@vps   # luego: ssh -p 2222 user@localhost desde el vps

# ProxyCommand cuando el bastión no tiene ssh (solo nc)
ssh -o ProxyCommand="ssh -W %h:%p user@bastion" user@interno

# Ejecutar sudo remoto que exige TTY
ssh -t user@host 'sudo systemctl restart nginx'
```

Ver también [[sshd]] · [[scp]] · [[sftp]] · [[proxychains]] · [[SSH_protips]].
