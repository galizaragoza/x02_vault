# ssh-keygen

**`ssh-keygen`** genera, gestiona y convierte claves de autenticación de OpenSSH. Crea pares de claves (Ed25519, RSA, ECDSA, FIDO/SK, post-cuántica), cambia passphrases, calcula y muestra huellas (fingerprints), manipula `known_hosts`, firma y verifica certificados SSH y ficheros arbitrarios, y genera listas de revocación (KRL). Es la herramienta central de todo el ciclo de vida de claves de la suite. Sintaxis base: `ssh-keygen [modo] [opciones]`.

Parte del índice de la suite: [[OpenSSH]].

---

## Generación de claves

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-t tipo` | Tipo de clave: `ed25519`, `rsa`, `ecdsa`, `ecdsa-sk`, `ed25519-sk`, `mldsa44-ed25519`. | `ssh-keygen -t ed25519 -C "pentest"` |
| `-b bits` | Longitud en bits (relevante en RSA: 2048/4096; ECDSA: 256/384/521). | `ssh-keygen -t rsa -b 4096` |
| `-N passphrase` | Passphrase de la nueva clave (`""` = sin passphrase). | `ssh-keygen -t ed25519 -N '' -f ./k` |
| `-C comment` | Comentario de la clave (típicamente `user@host`). | `ssh-keygen -t ed25519 -C "ops@2026"` |
| `-f filename` | Ruta del fichero de clave a crear/usar. | `ssh-keygen -t ed25519 -f ~/.ssh/deploy` |
| `-a rounds` | Rondas KDF (endurece contra fuerza bruta de la passphrase). | `ssh-keygen -t ed25519 -a 100` |
| `-o` | Fuerza el nuevo formato de clave privada OpenSSH (implícito en modernas). | `ssh-keygen -o -t rsa -b 4096` |
| `-w provider` | Librería del autenticador FIDO para claves `*-sk`. | `ssh-keygen -t ed25519-sk -w /usr/lib/libsk.so` |
| `-Z cipher` | Cifra la clave privada con un algoritmo concreto. | `ssh-keygen -Z aes256-gcm@openssh.com -t rsa` |
| `-A` | Genera todas las claves de host por defecto que falten (setup de servidor). | `ssh-keygen -A` |

---

## Gestión de claves existentes

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-p` | Cambia la passphrase de una clave existente. | `ssh-keygen -p -f ~/.ssh/id_ed25519` |
| `-c` | Cambia el comentario de la clave. | `ssh-keygen -c -f ~/.ssh/id_ed25519 -C nuevo` |
| `-y` | Deriva la clave **pública** a partir de la privada. | `ssh-keygen -y -f id_rsa > id_rsa.pub` |
| `-P passphrase` | Passphrase antigua (para operaciones no interactivas). | `ssh-keygen -p -P old -N new -f key` |
| `-U` | Indica que la CA reside en un `ssh-agent` (con `-s`/`-Y sign`). | `ssh-keygen -Us ca.pub -I id user.pub` |

---

## Huellas (fingerprints) y visualización

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-l` | Muestra la huella de una clave pública/privada. | `ssh-keygen -lf id_ed25519.pub` |
| `-E hash` | Algoritmo de la huella: `sha256` (default) o `md5`. | `ssh-keygen -lE md5 -f key.pub` |
| `-B` | Muestra el digest "bubblebabble" (más fácil de leer/comparar). | `ssh-keygen -B -f id_rsa` |
| `-v` | Verbose; con `-l` dibuja el arte ASCII (randomart). | `ssh-keygen -lv -f id_ed25519.pub` |

---

## Conversión de formatos

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-e` | Exporta una clave OpenSSH a otro formato (`-m`). | `ssh-keygen -e -m PEM -f id_rsa.pub` |
| `-i` | Importa una clave en formato externo a OpenSSH. | `ssh-keygen -i -m PKCS8 -f ext.pub` |
| `-m formato` | Formato objetivo: `RFC4716`, `PKCS8`, `PEM`. | `ssh-keygen -p -m PEM -f id_rsa` |
| `-D pkcs11` | Descarga claves públicas de un token PKCS#11. | `ssh-keygen -D /usr/lib/opensc-pkcs11.so` |
| `-K` | Descarga claves residentes de un autenticador FIDO. | `ssh-keygen -K` |

---

## known_hosts

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-F hostname` | Busca un host en `known_hosts`. | `ssh-keygen -F 10.10.10.10` |
| `-R hostname` | Elimina las entradas de un host de `known_hosts`. | `ssh-keygen -R 192.168.1.100` |
| `-H` | Hashea los nombres/direcciones de `known_hosts`. | `ssh-keygen -H -f ~/.ssh/known_hosts` |

---

## Certificados SSH (CA)

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-s ca_key` | Firma una clave pública como certificado usando la CA. | `ssh-keygen -s ca -I alice -n alice user.pub` |
| `-I identity` | Identidad del certificado (para logs y `-n`). | `ssh-keygen -s ca -I "alice@2026" user.pub` |
| `-n principals` | Principales (usuarios/hosts) válidos del certificado. | `ssh-keygen -s ca -I id -n root,admin user.pub` |
| `-h` | Emite un certificado **de host** en vez de de usuario. | `ssh-keygen -s ca -h -I web01 host.pub` |
| `-V validity_interval` | Ventana de validez (`+52w`, `-1d:+1d`). | `ssh-keygen -s ca -V +52w -I id user.pub` |
| `-z serial` | Número de serie del certificado. | `ssh-keygen -s ca -z 42 -I id user.pub` |
| `-O option` | Opción del certificado (`force-command`, `no-pty`, `source-address`...). | `ssh-keygen -s ca -O no-pty -I id user.pub` |
| `-L` | Imprime el contenido de un certificado. | `ssh-keygen -L -f user-cert.pub` |

---

## Revocación (KRL) y firma de ficheros

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-k` | Genera una KRL (Key Revocation List). | `ssh-keygen -k -f revoked.krl bad.pub` |
| `-u` | Actualiza (añade a) una KRL existente. | `ssh-keygen -ku -f revoked.krl new.pub` |
| `-Q` | Comprueba si una clave está revocada en una KRL. | `ssh-keygen -Q -f revoked.krl user.pub` |
| `-Y sign` | Firma ficheros con una clave (namespace obligatorio). | `ssh-keygen -Y sign -f key -n file doc.txt` |
| `-Y verify` | Verifica una firma. | `ssh-keygen -Y verify -f allowed -I id -n file -s doc.txt.sig < doc.txt` |
| `-Y find-principals` | Busca los principales de una firma en `allowed_signers`. | `ssh-keygen -Y find-principals -s sig -f allowed` |
| `-Y check-novalidate` | Verifica estructura de la firma sin validar firmante. | `ssh-keygen -Y check-novalidate -n file -s doc.sig` |
| `-n principals` | Namespace / principales para operaciones `-Y`. | `ssh-keygen -Y sign -n git -f key commit` |

---

## Cálculo de parámetros Diffie-Hellman / moduli

| Flag | Función | Ejemplo |
| --- | --- | --- |
| `-M generate` | Genera candidatos para el fichero `moduli`. | `ssh-keygen -M generate -O bits=4096 cand` |
| `-M screen` | Criba (screening) candidatos de `moduli`. | `ssh-keygen -M screen -f cand moduli` |
| `-r hostname` | Genera registros DNS SSHFP para el host. | `ssh-keygen -r host -f ssh_host_ed25519_key.pub` |
| `-g` | Formato DNS genérico al usar `-r`. | `ssh-keygen -g -r host -f key.pub` |
| `-q` | Modo silencioso. | `ssh-keygen -q -t ed25519 -N '' -f k` |

---

## Casos prácticos

```bash
# Par Ed25519 sin passphrase para automatización
ssh-keygen -t ed25519 -N '' -C "deploy@ci" -f ./deploy_key

# Crackear passphrase de una clave (extracción → john)
ssh2john id_rsa > hash.txt && john --wordlist=rockyou.txt hash.txt

# Convertir clave nueva a PEM (herramientas legacy)
ssh-keygen -p -m PEM -f id_rsa

# Limpiar host tras reinstalar (host key changed)
ssh-keygen -R 192.168.1.100
```

Ver también [[ssh]] · [[sshd]] · [[ssh-add]] · [[JohnTheRipper]] · [[hashcat]].
