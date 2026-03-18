# ssh
| **Parámetro**     | **Función**                                                                        | **Ejemplo de Sintaxis**                           |
| ----------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------- |
| `-p`              | Especifica el puerto de conexión (por defecto es el 22).                           | `ssh -p 2222 user@host`                           |
| `-i`              | Selecciona el archivo de identidad (clave privada) para autenticación.             | `ssh -i ~/.ssh/id_rsa user@host`                  |
| `-L`              | **Local Port Forwarding**: Redirige un puerto local al host remoto.                | `ssh -L 8080:localhost:80 user@host`              |
| `-R`              | **Remote Port Forwarding**: Redirige un puerto remoto a la máquina local.          | `ssh -R 9090:localhost:3000 user@host`            |
| `-D`              | **Dynamic Port Forwarding**: Crea un túnel SOCKS (Proxy).                          | `ssh -D 1080 user@host`                           |
| `-C`              | Comprime todos los datos enviados (útil en conexiones lentas).                     | `ssh -C user@host`                                |
| `-X`              | Habilita el reenvío de la interfaz gráfica (X11 Forwarding).                       | `ssh -X user@host`                                |
| `-v / -vv / -vvv` | Modo verbose para depuración (más "v" aumentan el detalle).                        | `ssh -vvv user@host`                              |
| `-N`              | No ejecuta comandos remotos (útil solo para túneles).                              | `ssh -N -L 8080:localhost:80 user@host`           |
| `-o`              | Permite pasar opciones de configuración específicas (ej. `StrictHostKeyChecking`). | `ssh -o "UserKnownHostsFile=/dev/null" user@host` |

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
