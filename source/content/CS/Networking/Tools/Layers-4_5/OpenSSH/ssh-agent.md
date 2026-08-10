# ssh-agent

**`ssh-agent`** es un demonio que mantiene en memoria las claves privadas ya descifradas para no reintroducir la passphrase en cada conexión. Se arranca una vez por sesión, exporta variables de entorno (`SSH_AUTH_SOCK`, `SSH_AGENT_PID`) que los clientes usan para pedirle firmas, y las claves se cargan/descargan con [[ssh-add]]. Habilita también el reenvío del agente (`ssh -A`) para autenticar saltos encadenados sin copiar claves. Sintaxis base: `ssh-agent [opciones] [comando [args]]`.

Parte del índice de la suite: [[OpenSSH]].

```
ssh-agent [-c | -s] [-Dd] [-a bind_addr] [-E hash] [-O opt]
          [-P providers] [-t life] [-Tu] [comando [args...]]
ssh-agent -k
```

---

## Arranque y salida de shell

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-s` | Genera comandos para shell Bourne (`sh`/`bash`/`zsh`). Default si `$SHELL` no es csh. | `eval $(ssh-agent -s)` |
| `-c` | Genera comandos para C-shell (`csh`/`tcsh`). | `eval \`ssh-agent -c\`` |
| `-D` | Foreground: no hace fork (útil en supervisores/contenedores). | `ssh-agent -D` |
| `-d` | Modo debug: foreground + salida de depuración. | `ssh-agent -d` |
| `-k` | Mata el agente indicado por `$SSH_AGENT_PID`. | `ssh-agent -k` |
| `comando [args]` | Lanza un comando con el agente activo solo durante su vida. | `ssh-agent zsh` |

---

## Socket, límites y providers

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-a bind_address` | Ruta del socket UNIX de escucha (en vez de una temporal). | `ssh-agent -a /tmp/agent.sock` |
| `-t life` | Vida máxima por defecto de las identidades añadidas (segundos o `1h30m`). | `ssh-agent -t 3600` |
| `-E fingerprint_hash` | Algoritmo de huella al mostrar claves: `sha256`/`md5`. | `ssh-agent -E md5` |
| `-P allowed_providers` | Patrón de librerías PKCS#11/FIDO permitidas (whitelist). | `ssh-agent -P /usr/lib/*` |
| `-O option` | Opción del agente, p. ej. `allow-remote-pkcs11`. | `ssh-agent -O allow-remote-pkcs11` |
| `-T` | Liga el socket en un subdirectorio aleatorio de `$TMPDIR` (aislamiento). | `ssh-agent -T` |
| `-u` | Limpia sockets obsoletos en `$HOME/.ssh/agent/` y sale. | `ssh-agent -u` |
| `-V` | Muestra la versión y sale. | `ssh-agent -V` |

---

## Variables de entorno exportadas

| Variable | Contenido |
| --- | --- |
| `SSH_AUTH_SOCK` | Ruta del socket que los clientes usan para hablar con el agente. |
| `SSH_AGENT_PID` | PID del agente (necesario para `ssh-agent -k`). |

---

## Casos prácticos

```bash
# Arranque típico en la shell actual
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_ed25519

# Agente con caducidad de 1h para las claves
eval $(ssh-agent -t 1h)

# Envolver un único comando con agente efímero
ssh-agent bash -c 'ssh-add key && ssh -A user@bastion'

# Matar el agente al terminar
ssh-agent -k
```

> **OpSec**: el reenvío del agente (`ssh -A`) expone tus firmas al host intermedio; un root malicioso en el bastión puede usar tus claves mientras la sesión esté abierta. Prefiere `ProxyJump` (`-J`) cuando sea posible.

Ver también [[ssh-add]] · [[ssh-keygen]] · [[ssh]] · [[SSH_protips]].
