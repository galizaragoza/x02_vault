#reference #Software #Ciberseguridad

**KeePass** = gestor de contraseñas offline. Guarda las credenciales en una base de datos cifrada (`.kdbx`) protegida por una **master password** (y opcionalmente keyfile + cuenta Windows). Forks: **KeePassXC** (multiplataforma), KeePass2.

# Modelo

| Elemento | Función |
|----------|---------|
| `.kdbx` | BBDD cifrada (AES-256 / ChaCha20, KDF Argon2/AES-KDF). |
| **Master password** | Clave que descifra toda la BBDD. |
| **Keyfile** | Fichero como factor adicional al password. |
| **Composite key** | Combinación de master + keyfile + cuenta de usuario. |

# En pentest / forense

El `.kdbx` es un objetivo de alto valor en post-explotación: una sola master password abre todas las credenciales.

```bash
# Crackear la master password offline
keepass2john Database.kdbx > hash.txt
hashcat -m 13400 hash.txt rockyou.txt
john hash.txt --wordlist=rockyou.txt
```

Buscar BBDD en loot: `find / -name '*.kdbx' 2>/dev/null`.

## CVE-2023-32784

Vulnerabilidad de KeePass 2.x que permite **recuperar la master password en claro** (salvo el primer carácter) desde un **volcado de memoria** del proceso. Restos del campo de entrada quedan en memoria gestionada de .NET.

> Requiere acceso al sistema donde KeePass corre (memory dump, hiberfil.sys, swap o crash dump). Mitigado en KeePass ≥ 2.54.

```bash
# Con la PoC sobre un dump de memoria
python3 keepass-dump-masterkey.py -d memory.dmp
```

# Recursos
### [Exploiting KeePass CVE-2023-32784](https://www.cyberis.com/article/exploiting-keepass-cve-2023-32784)
### [KeePassXC](https://keepassxc.org/) · [Hashcat mode 13400](https://hashcat.net/wiki/doku.php?id=example_hashes)
