# ssh-add

**`ssh-add`** gestiona las identidades (claves privadas y certificados) que mantiene un [[ssh-agent]] en ejecución. Añade claves al agente descifrándolas una sola vez, las lista, las borra, fija su tiempo de vida y bloquea/desbloquea el propio agente. Se comunica con el agente a través del socket apuntado por `SSH_AUTH_SOCK`. Sin argumentos, añade las claves por defecto (`~/.ssh/id_*`). Sintaxis base: `ssh-add [opciones] [fichero...]`.

Parte del índice de la suite: [[OpenSSH]].

```
ssh-add [-cDdKkLlqvXx] [-E hash] [-H hostkey] [-h destino] [-S provider]
        [-s pkcs11] [-t life] [-T pubkey...] [fichero...]
```

---

## Añadir y borrar identidades

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `ssh-add fichero` | Añade la clave indicada al agente. | `ssh-add ~/.ssh/id_ed25519` |
| `-d` | Elimina la identidad indicada (en vez de añadirla). | `ssh-add -d ~/.ssh/id_ed25519` |
| `-D` | Elimina **todas** las identidades del agente. | `ssh-add -D` |
| `-k` | Procesa solo claves privadas planas (ignora certificados). | `ssh-add -k ~/.ssh/id_rsa` |
| `-C` | Procesa solo certificados (ignora claves planas). | `ssh-add -C ~/.ssh/id_rsa-cert.pub` |
| `-t life` | Caducidad de la identidad añadida (segundos o `1h`). | `ssh-add -t 3600 ~/.ssh/id_ed25519` |
| `-c` | Exige confirmación (askpass) en cada uso de la clave. | `ssh-add -c ~/.ssh/id_ed25519` |
| `-h destination_constraint` | Restringe la clave a hosts concretos (constraint de destino). | `ssh-add -h user@web01 key` |
| `-N` | Al añadir certificados, no auto-borra el certificado con la clave. | `ssh-add -N key-cert.pub` |

---

## Listar y consultar

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-l` | Lista las huellas de las identidades cargadas. | `ssh-add -l` |
| `-L` | Lista los parámetros de clave pública completos. | `ssh-add -L` |
| `-E fingerprint_hash` | Algoritmo de huella para `-l`: `sha256`/`md5`. | `ssh-add -l -E md5` |
| `-Q` | Consulta las extensiones de protocolo que soporta el agente. | `ssh-add -Q` |
| `-T pubkey ...` | Prueba que el agente puede firmar con esas claves públicas. | `ssh-add -T id_ed25519.pub` |

---

## PKCS#11 / FIDO / hardware

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-s pkcs11` | Añade claves de una librería PKCS#11 (smartcard/token). | `ssh-add -s /usr/lib/opensc-pkcs11.so` |
| `-e pkcs11` | Elimina claves de una librería PKCS#11. | `ssh-add -e /usr/lib/opensc-pkcs11.so` |
| `-S provider` | Provider de firma para claves FIDO (`*-sk`). | `ssh-add -S /usr/lib/libsk.so key` |
| `-K` | Carga claves residentes desde un autenticador FIDO. | `ssh-add -K` |
| `-H hostkey_file` | Fichero de claves de host para constraints de destino. | `ssh-add -H known_hosts -h user@h key` |

---

## Bloqueo del agente y diagnóstico

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-x` | Bloquea el agente con una contraseña (no firma hasta desbloquear). | `ssh-add -x` |
| `-X` | Desbloquea el agente. | `ssh-add -X` |
| `-q` | Silencioso tras una operación correcta. | `ssh-add -q key` |
| `-v` | Verbose (repetible) para depurar. | `ssh-add -vv key` |

---

## Casos prácticos

```bash
# Cargar clave con caducidad y confirmación por uso
ssh-add -c -t 1h ~/.ssh/id_ed25519

# Ver qué hay cargado y purgar todo
ssh-add -l
ssh-add -D

# Bloquear el agente al alejarte del equipo
ssh-add -x   # ... ssh-add -X para volver
```

Ver también [[ssh-agent]] · [[ssh-keygen]] · [[ssh]].
