# ssh-keyscan

**`ssh-keyscan`** recolecta las claves públicas de host de uno o varios servidores SSH sin necesidad de autenticarse. Su uso principal es poblar `known_hosts` de forma masiva (automatización, bastiones, CI/CD) y, en reconocimiento, enumerar qué hosts de un rango tienen SSH abierto y qué tipos de clave y banners exponen. Es no interactivo, paraleliza y admite listas de entrada. Sintaxis base: `ssh-keyscan [opciones] [host | addrlist ...]`.

Parte del índice de la suite: [[OpenSSH]].

```
ssh-keyscan [-46cDHv] [-f file] [-O option] [-p port] [-T timeout]
            [-t type] [host | addrlist namelist ...]
```

---

## Selección de hosts y red

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-4` / `-6` | Fuerza IPv4 / IPv6. | `ssh-keyscan -4 10.0.0.5` |
| `-f file` | Lee la lista de hosts desde fichero (`-` = stdin). | `ssh-keyscan -f hosts.txt` |
| `-p port` | Puerto SSH a escanear (default 22). | `ssh-keyscan -p 2222 10.0.0.5` |
| `-T timeout` | Timeout de conexión por host (segundos). | `ssh-keyscan -T 2 -f hosts.txt` |

---

## Tipo de clave y salida

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-t type` | Tipos de clave a solicitar: `ed25519`, `rsa`, `ecdsa`, `dsa` (lista con comas). | `ssh-keyscan -t ed25519,rsa host` |
| `-c` | Solicita **certificados** de host en vez de claves planas. | `ssh-keyscan -c host` |
| `-H` | Hashea los nombres/direcciones en la salida (compatible con `known_hosts` hasheado). | `ssh-keyscan -H host >> ~/.ssh/known_hosts` |
| `-D` | Imprime las claves como registros DNS **SSHFP**. | `ssh-keyscan -D host` |
| `-O option` | Opción avanzada, p. ej. `hashalg=sha256` para SSHFP. | `ssh-keyscan -D -O hashalg=sha256 host` |
| `-v` | Verbose (depuración del proceso). | `ssh-keyscan -v host` |

---

## Casos prácticos

```bash
# Poblar known_hosts de un host concreto (hasheado)
ssh-keyscan -H 10.0.0.20 >> ~/.ssh/known_hosts

# Escanear un rango y quedarse solo con los que responden SSH
ssh-keyscan -T 2 -t ed25519,rsa 10.0.0.0/24 2>/dev/null

# Recon: enumerar banners y tipos de clave de una lista
ssh-keyscan -f targets.txt -v 2>&1 | grep -Ei 'SSH-|key type'

# Generar registros DNS SSHFP para publicar en la zona
ssh-keyscan -D -O hashalg=sha256 host.dominio.com
```

> En reconocimiento sustituye a un `nmap -p22` cuando además interesa el tipo de clave/banner; combínalo con `-T` bajo para barridos rápidos.

Ver también [[ssh]] · [[ssh-keygen]] · [[ss]] · [[netstat]].
