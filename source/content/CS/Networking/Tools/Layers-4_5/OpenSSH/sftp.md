# sftp

**`sftp`** es el cliente interactivo de transferencia de ficheros de OpenSSH sobre el subsistema SFTP (SSH File Transfer Protocol). Ofrece una sesión tipo FTP —con navegación de directorios, `get`/`put`, permisos y reanudación— pero íntegramente cifrada y autenticada sobre SSH. Sirve tanto en modo interactivo como por lotes (`-b`) para scripts. Sintaxis base: `sftp [opciones] [user@]host[:ruta]`.

Parte del índice de la suite: [[OpenSSH]].

```
sftp [-46aCfNpqrv] [-B buffer] [-b batchfile] [-c cipher] [-D server_cmd]
     [-F ssh_config] [-i identity] [-J destino] [-l limit] [-o ssh_option]
     [-P port] [-R num_requests] [-S program] [-s subsystem] [-X sftp_opt] destino
```

---

## Conexión y autenticación

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-4` / `-6` | Fuerza IPv4 / IPv6. | `sftp -4 user@host` |
| `-P port` | Puerto SSH del servidor. | `sftp -P 2222 user@host` |
| `-i identity_file` | Clave privada específica. | `sftp -i ~/.ssh/id_ed25519 user@host` |
| `-F ssh_config` | Fichero de configuración SSH alternativo. | `sftp -F ./ops.cfg user@host` |
| `-o ssh_option` | Opción de `ssh_config(5)` en línea. | `sftp -o StrictHostKeyChecking=no user@host` |
| `-J destino` | ProxyJump (salto por bastión). | `sftp -J user@bastion user@internal` |
| `-A` | Reenvía el `ssh-agent` al remoto. | `sftp -A user@host` |
| `-S program` | Programa para la conexión cifrada. | `sftp -S /usr/bin/ssh user@host` |
| `-s subsystem` | Subsistema/servidor SFTP a solicitar. | `sftp -s sftp user@host` |
| `-D server_command` | Conecta a un servidor SFTP local vía este comando (sin ssh). | `sftp -D /usr/lib/openssh/sftp-server` |

---

## Transferencia, rendimiento y modo batch

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-b batchfile` | Ejecuta comandos desde fichero y sale (scripting). `-` = stdin. | `sftp -b cmds.txt user@host` |
| `-r` | Copia recursiva de directorios (con `get`/`put`). | `sftp -r user@host:/var/www ./` |
| `-p` | Preserva mtime, atime y permisos. | `sftp -p user@host` |
| `-a` | Reanuda transferencias interrumpidas en vez de sobrescribir. | `sftp -a user@host` |
| `-f` | Fuerza flush a disco tras cada transferencia. | `sftp -f user@host` |
| `-C` | Habilita compresión. | `sftp -C user@host` |
| `-c cipher` | Fuerza algoritmo de cifrado. | `sftp -c aes128-ctr user@host` |
| `-l limit` | Limita el ancho de banda (Kbit/s). | `sftp -l 4096 user@host` |
| `-B buffer_size` | Tamaño de buffer de transferencia (default 32 KB). | `sftp -B 65536 user@host` |
| `-R num_requests` | Peticiones concurrentes en vuelo (default 64). | `sftp -R 128 user@host` |
| `-X sftp_option` | Ajustes finos del protocolo: `nrequests=N`, `buffer=N`. | `sftp -X nrequests=256 user@host` |

---

## Salida y diagnóstico

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-q` | Modo silencioso (sin barra de progreso ni warnings). | `sftp -q user@host` |
| `-N` | Deshabilita el modo silencioso implícito de `-b`. | `sftp -N -b cmds.txt user@host` |
| `-v` | Sube el nivel de log (repetible). | `sftp -vvv user@host` |

---

## Comandos interactivos (dentro de la sesión)

| Comando | Función | Ejemplo |
| --- | --- | --- |
| `get [-afPpr] remoto [local]` | Descarga fichero/directorio. | `get -r /var/www/html` |
| `put [-afPpr] local [remoto]` | Sube fichero/directorio. | `put -r loot/` |
| `reget` / `reput` | Reanuda descarga / subida. | `reget bigfile.iso` |
| `ls [-1afhlnrSt]` | Lista el directorio remoto. | `ls -la` |
| `lls` | Lista el directorio **local**. | `lls` |
| `cd` / `lcd` | Cambia directorio remoto / local. | `lcd /tmp` |
| `pwd` / `lpwd` | Muestra directorio remoto / local. | `pwd` |
| `mkdir` / `rmdir` | Crea / borra directorio remoto. | `mkdir /tmp/exfil` |
| `rm` / `rename` | Borra / renombra remoto. | `rm /tmp/old.log` |
| `chmod` / `chown` / `chgrp` | Cambia permisos/propietario remoto. | `chmod 600 id_rsa` |
| `ln [-s]` | Crea enlace (simbólico con `-s`). | `ln -s /etc/passwd pw` |
| `df [-hi]` | Espacio libre del filesystem remoto. | `df -h` |
| `progress` | Alterna la barra de progreso. | `progress` |
| `!comando` | Ejecuta un comando en el shell **local**. | `!tar czf loot.tgz .` |
| `bye` / `quit` / `exit` | Cierra la sesión. | `bye` |
| `help` / `?` | Ayuda de comandos. | `help` |

---

## Casos prácticos

```bash
# Descarga recursiva del webroot
sftp user@host <<'EOF'
get -r /var/www/html ./loot
bye
EOF

# Batch scriptado (exfil no interactiva)
printf 'get -r /home/user/.ssh\nbye\n' > cmds.txt
sftp -b cmds.txt -i id_rsa user@target

# Transferencia rápida en enlace de alta latencia
sftp -X nrequests=256 -B 262144 user@host
```

Ver también [[ssh]] · [[scp]] · [[ftp]] · [[SSHFS]].
