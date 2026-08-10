# sshd (servidor / daemon)

**`sshd`** es el demonio servidor de OpenSSH: escucha conexiones (por defecto en el puerto 22/tcp), autentica clientes y les da shell, ejecución de comandos, transferencia de ficheros y reenvíos. Normalmente se arranca vía systemd (`sshd.service`) y lee su configuración de `/etc/ssh/sshd_config`. Se invoca directamente sobre todo para depurar arranque, validar configuración o levantar una instancia en un puerto alternativo. La sintaxis base es `sshd [opciones]`.

Parte del índice de la suite: [[OpenSSH]].

```
sshd [-46DdeiqTt] [-C connspec] [-c host_cert] [-E log] [-f config]
     [-g grace] [-h host_key] [-o option] [-p port] [-u len] [-V]
```

---

## Ejecución y modo de arranque

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-D` | No se desacopla ni se hace daemon (foreground). Ideal bajo systemd/contenedores. | `sshd -D` |
| `-d` | Modo debug: foreground, verbose a stderr, atiende una sola conexión. Repetible (`-ddd`). | `sshd -ddd -p 2222` |
| `-e` | Envía logs de debug a stderr en vez del syslog. | `sshd -d -e` |
| `-i` | Arranca desde `inetd` (una conexión por proceso). | `sshd -i` |
| `-q` | Modo silencioso: nada al syslog. | `sshd -q` |

---

## Red y claves

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-4` | Escucha solo en IPv4. | `sshd -4` |
| `-6` | Escucha solo en IPv6. | `sshd -6` |
| `-p port` | Puerto de escucha. Repetible para varios puertos. | `sshd -p 2222 -p 22` |
| `-h host_key_file` | Fichero de clave de host a usar. Repetible por tipo. | `sshd -h /etc/ssh/ssh_host_ed25519_key` |
| `-c host_certificate_file` | Certificado de host que acompaña a la clave (`-h`). | `sshd -h key -c key-cert.pub` |
| `-u len` | Tamaño del campo host en `utmp`. `-u0` guarda solo IP (no DNS). | `sshd -u0` |

---

## Configuración

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-f config_file` | Fichero de configuración alternativo. | `sshd -f /etc/ssh/sshd_hardened.conf` |
| `-o option` | Opción de `sshd_config(5)` en línea (anula el fichero). Repetible. | `sshd -o "PermitRootLogin no" -o "Port 2222"` |
| `-g login_grace_time` | Tiempo máximo para autenticar antes de desconectar (default 120s). | `sshd -g 30` |

---

## Validación y test

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-t` | Test mode: valida sintaxis del config y cordura de claves, sin arrancar. | `sshd -t` |
| `-T` | Extended test: imprime la **configuración efectiva** resuelta y sale. | `sshd -T` |
| `-C connection_spec` | Contexto (`user=`, `host=`, `addr=`...) para evaluar bloques `Match` con `-T`. | `sshd -T -C user=root,host=x,addr=1.2.3.4` |
| `-G` | Parsea e imprime la configuración sin validar claves ni resolver DNS. | `sshd -G` |
| `-V` | Muestra la versión y sale. | `sshd -V` |

---

## Opciones `sshd_config` clave (hardening)

| Directiva | Función | Valor recomendado |
| --- | --- | --- |
| `PermitRootLogin` | Login directo de root. | `no` / `prohibit-password` |
| `PasswordAuthentication` | Autenticación por contraseña. | `no` (solo claves) |
| `PubkeyAuthentication` | Autenticación por clave pública. | `yes` |
| `PermitEmptyPasswords` | Permite cuentas sin contraseña. | `no` |
| `AllowUsers` / `AllowGroups` | Whitelist de acceso. | listar cuentas explícitas |
| `X11Forwarding` | Reenvío X11 del servidor. | `no` salvo necesidad |
| `AllowTcpForwarding` | Reenvío de puertos. | `no` si no se usa pivoting |
| `MaxAuthTries` | Intentos de auth por conexión. | `3` |
| `ClientAliveInterval` | Keepalive/timeout de sesiones inactivas. | `300` |
| `Banner` | Banner legal pre-login. | ruta a fichero |

---

## Casos prácticos

```bash
# Validar antes de reiniciar el servicio (evita quedarte fuera)
sshd -t && systemctl restart sshd

# Ver la config efectiva de un usuario/origen concreto (depurar Match)
sshd -T -C user=deploy,addr=10.0.0.5,host=web01 | grep -i forward

# Instancia de depuración en puerto alterno, una conexión, verbose
sshd -ddd -p 2222

# Levantar sin fichero, forzando opciones por CLI
sshd -o "PasswordAuthentication no" -o "PermitRootLogin no" -p 2222
```

Ver también [[ssh]] · [[ssh-keygen]] · [[Hardening]] · [[SSH_persistencia]].
