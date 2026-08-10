# ssh-copy-id

**`ssh-copy-id`** instala una clave pública en el fichero `~/.ssh/authorized_keys` de una cuenta remota, habilitando el login por clave sin volver a introducir contraseña. Es un script que se conecta con [[ssh]] (usando la autenticación disponible, típicamente contraseña la primera vez), evita duplicar claves ya presentes y ajusta permisos del directorio `.ssh`. Sintaxis base: `ssh-copy-id [opciones] [user@]host`.

Parte del índice de la suite: [[OpenSSH]].

```
ssh-copy-id [-f] [-n] [-s] [-x] [-i [identity_file]] [-p port]
            [-t target_path] [-F ssh_config | -o ssh_option] [user@]host
```

---

## Selección de clave y destino

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-i [identity_file]` | Clave pública a instalar (default: la del agente o `~/.ssh/id_*.pub`). | `ssh-copy-id -i id_ed25519.pub user@target` |
| `-p port` | Puerto SSH del host remoto. | `ssh-copy-id -p 2222 user@10.10.10.10` |
| `-t target_path` | Ruta del `authorized_keys` remoto (default `~/.ssh/authorized_keys`). | `ssh-copy-id -t ~/.ssh/keys user@host` |

---

## Modo de operación

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-f` | Modo forzado: instala sin comprobar si la clave ya existe. | `ssh-copy-id -f -i k.pub user@host` |
| `-n` | Dry-run: imprime las claves que instalaría, sin instalarlas. | `ssh-copy-id -n -i k.pub user@host` |
| `-s` | Modo SFTP: instala vía SFTP en vez de ejecutar comandos de shell. | `ssh-copy-id -s -i k.pub user@host` |

---

## Conexión SSH y diagnóstico

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-F ssh_config` | Fichero de configuración SSH alternativo. | `ssh-copy-id -F ./ops.cfg user@host` |
| `-o ssh_option` | Opción de `ssh_config(5)` en línea (ProxyJump, StrictHostKeyChecking...). | `ssh-copy-id -o "ProxyJump bastion" user@internal` |
| `-x` | Depura el propio script (`set -x` del shell). | `ssh-copy-id -x -i k.pub user@host` |
| `-h` / `-?` | Muestra el resumen de uso. | `ssh-copy-id -h` |

---

## Casos prácticos

```bash
# Instalar clave la primera vez (pedirá la contraseña remota)
ssh-copy-id -i ~/.ssh/id_ed25519.pub deploy@10.0.0.20

# A través de un bastión, en puerto no estándar
ssh-copy-id -p 2222 -o "ProxyJump user@bastion" -i k.pub user@internal

# Ver qué se instalaría sin tocar el remoto
ssh-copy-id -n -i k.pub user@host

# Persistencia manual equivalente (sin el binario)
cat id.pub | ssh user@host 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

> **Nota de seguridad**: instalar una clave en `authorized_keys` es una técnica común de **persistencia** post-explotación. Ver [[SSH_persistencia]].

Ver también [[ssh]] · [[ssh-keygen]] · [[ssh-agent]] · [[SSH_persistencia]].
