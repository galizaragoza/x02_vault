#reference #Criptografia

**SHA** (Secure Hash Algorithm) = familia de funciones hash criptográficas del NIST. Entrada de tamaño arbitrario → digest de tamaño fijo. Propiedades: determinista, unidireccional, resistente a colisiones y a preimagen.

# Familias

| Algoritmo | Salida | Estado | Construcción |
|-----------|--------|--------|--------------|
| **SHA-1** | 160 bits (40 hex) | ❌ **Roto** (colisiones) | Merkle–Damgård |
| **SHA-224 / 256** | 224 / 256 bits | ✅ Seguro | Merkle–Damgård |
| **SHA-384 / 512** | 384 / 512 bits | ✅ Seguro | Merkle–Damgård |
| **SHA-3** (Keccak) | 224–512 bits | ✅ Seguro | Esponja (sponge) |

# SHA-1

Deprecado e inseguro, pero aún abundante (Git, certificados antiguos, firmas legacy). Produce 160 bits / 40 hex.

- **2017 — SHAttered** (Google): primera colisión real de SHA-1.
- **2020 — chosen-prefix collision**: colisiones con prefijos elegidos → falsificación práctica de certificados/PGP.

> SHA-2 y SHA-3 siguen seguros. SHA-256 es el estándar de facto (TLS, blockchain, integridad).

# Diferencia con HMAC y password hashing

- Un hash **a secas no sirve para contraseñas**: es demasiado rápido (GPU cracking masivo). Para passwords usar **bcrypt / scrypt / Argon2** (lentos + salt).
- Para integridad/autenticación de mensajes con clave → **HMAC-SHA256**.

# Comandos

```bash
sha1sum   fichero
sha256sum fichero
sha512sum fichero
echo -n "texto" | sha256sum          # hash de una cadena

# Verificar integridad
sha256sum -c checksums.txt

# HMAC
openssl dgst -sha256 -hmac "clave" fichero
```

# Cracking

```bash
hashcat -m 100  hash.txt rockyou.txt    # SHA1
hashcat -m 1400 hash.txt rockyou.txt    # SHA256
hashcat -m 1700 hash.txt rockyou.txt    # SHA512
john --format=raw-sha256 hash.txt --wordlist=rockyou.txt
```

Identificar un hash desconocido: `hashid`, `hash-identifier`, `nth` (name-that-hash).

# Recursos
### [[!MD5]] · [[!Digital Sign]]
### [Wikipedia — SHA-1](https://en.wikipedia.org/wiki/SHA-1) · [Chosen-prefix collision](https://en.wikipedia.org/wiki/Collision_attack#Chosen-prefix_collision_attack)
### [SHAttered](https://shattered.io/) · [Hashcat — modos](https://hashcat.net/wiki/doku.php?id=example_hashes)
