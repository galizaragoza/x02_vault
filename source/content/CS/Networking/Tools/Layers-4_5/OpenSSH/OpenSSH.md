# OpenSSH — índice de la suite

**OpenSSH** es la implementación libre del protocolo **SSH (Secure Shell)**, que cifra la comunicación entre dos hosts sobre una red no confiable. Cubre acceso remoto interactivo, ejecución de comandos, transferencia de ficheros, reenvío de puertos (tunneling) y gestión de claves criptográficas. La suite se reparte en varios binarios: un cliente (`ssh`), un servidor (`sshd`) y utilidades auxiliares de copia, claves y agente. Estas notas documentan **cada binario por separado**; las opciones de configuración persistente viven en `ssh_config(5)` y `sshd_config(5)`.

> Versión de referencia: **OpenSSH_10.4p1**. Flags marcadas como recientes pueden no existir en releases antiguas (Debian oldstable, appliances).

---

## Binarios de la suite

| Binario | Rol | Nota |
| --- | --- | --- |
| `ssh` | Cliente: login remoto, ejecución, tunneling, ProxyJump | [[ssh]] |
| `sshd` | Servidor / daemon SSH | [[sshd]] |
| `scp` | Copia de ficheros sobre SSH (backend SFTP desde v9) | [[scp]] |
| `sftp` | Cliente interactivo de transferencia de ficheros | [[sftp]] |
| `ssh-keygen` | Generación, conversión y gestión de claves y certificados | [[ssh-keygen]] |
| `ssh-agent` | Demonio que cachea claves privadas descifradas | [[ssh-agent]] |
| `ssh-add` | Añade / lista / borra claves del agente | [[ssh-add]] |
| `ssh-copy-id` | Instala una clave pública en `authorized_keys` remoto | [[ssh-copy-id]] |
| `ssh-keyscan` | Recolecta claves públicas de host (poblar `known_hosts`) | [[ssh-keyscan]] |

---

## Ficheros clave

| Ruta | Contenido |
| --- | --- |
| `~/.ssh/config` | Configuración de cliente por host (`Host`, `Match`) |
| `~/.ssh/known_hosts` | Claves de host confiadas (verificación anti-MITM) |
| `~/.ssh/authorized_keys` | Claves públicas autorizadas a entrar a esta cuenta |
| `~/.ssh/id_ed25519[.pub]` | Par de claves por defecto (privada / pública) |
| `/etc/ssh/sshd_config` | Configuración del servidor |
| `/etc/ssh/ssh_host_*` | Claves de host del servidor |

---

## Relacionado

[[SSHFS]] · [[proxychains]] · [[OpenSSL]] · [[SSH_persistencia]] · [[SSH_protips]] · [[ss]] · [[netstat]]
