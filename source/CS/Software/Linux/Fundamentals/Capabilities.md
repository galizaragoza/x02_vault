#reference #Software

**Linux capabilities** = desglose fino de los privilegios del antiguo "todopoderoso root". En vez de dar todo o nada, se concede solo la porción necesaria. Un proceso con `CAP_NET_ADMIN` puede tocar el stack de red (cambiar IP, bind a puertos <1024, modo promiscuo) pero **no** montar filesystems (eso requiere `CAP_SYS_ADMIN`).

> "Think of capabilities as a fine-grained breakdown of the permissions classically attributed to the all-powerful root user on Linux." Reemplazan al modelo SUID-root para principio de mínimo privilegio.

# Capabilities frecuentes

| Capability | Permite |
|------------|---------|
| `CAP_NET_ADMIN` | Configurar red, modo promiscuo. |
| `CAP_NET_BIND_SERVICE` | Bind a puertos <1024 sin ser root. |
| `CAP_NET_RAW` | Sockets raw (ping, sniffing). |
| `CAP_SYS_ADMIN` | "Casi root": montar FS, namespaces… (la más peligrosa). |
| `CAP_SYS_PTRACE` | Depurar/inyectar en otros procesos. |
| `CAP_DAC_OVERRIDE` | Saltarse permisos de lectura/escritura. |
| `CAP_DAC_READ_SEARCH` | Leer cualquier fichero. |
| `CAP_SETUID` / `CAP_SETGID` | Cambiar UID/GID. |
| `CAP_CHOWN` | Cambiar propietario de ficheros. |

# Ver y asignar

```bash
# Ver capabilities de binarios en el sistema
getcap -r / 2>/dev/null

# De un proceso por PID
grep Cap /proc/$PID/status      # decodificar con: capsh --decode=00000...
capsh --print                   # capabilities del shell actual

# Asignar / quitar a un binario
sudo setcap cap_net_raw+ep /usr/bin/miprog
sudo setcap -r /usr/bin/miprog
```

# Sets (conjuntos)

| Set | Significado |
|-----|-------------|
| **Permitted (p)** | Lo que el proceso *puede* activar. |
| **Effective (e)** | Lo activo ahora. |
| **Inheritable (i)** | Lo que se hereda al `exec`. |

`cap_net_raw+ep` = permitida + efectiva.

# Privesc (ofensivo)

Capabilities mal asignadas son vía directa a root. Ver explotación detallada en [[LinPE_cheatsheet]].

| Capability en binario | Explotación |
|-----------------------|-------------|
| `cap_setuid+ep` | `binario -c 'import os; os.setuid(0); os.system("/bin/sh")'` (python) |
| `cap_dac_read_search` | Leer `/etc/shadow`. |
| `cap_dac_override` | Sobrescribir `/etc/passwd`. |

# Recursos
### [[LinPE_cheatsheet]] · [[Hardening]]
### [man capabilities(7)](https://man7.org/linux/man-pages/man7/capabilities.7.html) · [GTFOBins](https://gtfobins.github.io/)
