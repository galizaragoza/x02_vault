#reference #Criptografia

**Firma digital** = prueba criptográfica de **autenticidad** (quién), **integridad** (no alterado) y **no repudio** (no puede negarlo). Se firma con la **clave privada** y se verifica con la **pública** — al revés que el cifrado.

```
Firmar:    firma = Sign(privada,  HASH(mensaje))
Verificar: Verify(publica, mensaje, firma) -> válida / inválida
```

> Nunca se firma el mensaje entero sino su **hash** (rápido y de tamaño fijo). Por eso la resistencia a colisiones del hash es parte de la seguridad de la firma.

# DSA (Digital Signature Algorithm)

Estándar NIST basado en el **logaritmo discreto** sobre un campo finito (módulo primo `p`).

| Elemento | Rol |
|----------|-----|
| `p, q, g` | Parámetros del dominio (públicos). |
| `x` | Clave privada. |
| `y = g^x mod p` | Clave pública. |
| `k` | **Nonce** aleatorio por firma. |

```
Firma (r, s):
  r = (g^k mod p) mod q
  s = k⁻¹ (H(m) + x·r) mod q
```

- Solo firma (no cifra). Claves grandes (2048–3072 bits).
- **Mismo talón de Aquiles que ECDSA:** reutilizar/predecir `k` revela la clave privada `x`.

# ECDSA (Elliptic Curve DSA)

Misma construcción sobre **curvas elípticas** → claves mucho más cortas (256 bits ≈ RSA-3072), firmas rápidas. Detalle completo en [[ECDSA]].

# Comparativa

| Algoritmo | Base matemática | Tamaño clave (≈128-bit sec) | Nota |
|-----------|-----------------|------------------------------|------|
| **RSA** (firma) | Factorización | 3072 bits | Verificación rápida, firma lenta. |
| **DSA** | Log. discreto (mod p) | 3072 bits | En desuso. |
| **ECDSA** | Log. discreto (curva) | 256 bits | Estándar en TLS/SSH/blockchain. |
| **EdDSA / Ed25519** | Curva (Edwards) | 256 bits | Nonce **determinista** → sin riesgo de `k`. Preferido. |

# Recursos
### [[ECDSA]] · [[PGP]] · [[!SHA]]
### [Wikipedia — Digital Signature Algorithm](https://en.wikipedia.org/wiki/Digital_Signature_Algorithm)
### [Wikipedia — EdDSA](https://en.wikipedia.org/wiki/EdDSA)
