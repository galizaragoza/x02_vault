#reference #Criptografia

**ECDSA** (Elliptic Curve Digital Signature Algorithm) = variante de DSA sobre **curvas elípticas**. Misma seguridad que RSA con claves mucho más cortas → firmas rápidas y compactas. Usado en TLS, SSH, JWT (`ES256`), Bitcoin/Ethereum.

# Idea

La seguridad descansa en el **problema del logaritmo discreto en curva elíptica (ECDLP)**: dado `Q = d·G`, hallar `d` es inviable.

| Elemento | Significado |
|----------|-------------|
| `G` | Punto generador de la curva (público). |
| `d` | Clave privada (entero aleatorio). |
| `Q = d·G` | Clave pública (punto). |
| `n` | Orden del grupo. |
| `k` | **Nonce** aleatorio por firma (¡crítico!). |

# Firma y verificación

```
Firmar (mensaje m, clave d):
  e = HASH(m)
  k = aleatorio en [1, n-1]
  (x1, y1) = k·G
  r = x1 mod n
  s = k⁻¹ (e + r·d) mod n
  firma = (r, s)

Verificar (m, firma (r,s), clave pública Q):
  e = HASH(m)
  u1 = e·s⁻¹ ;  u2 = r·s⁻¹
  (x1,y1) = u1·G + u2·Q
  válida si  r ≡ x1 (mod n)
```

# Vulnerabilidades clave

| Fallo | Consecuencia |
|-------|--------------|
| **Nonce `k` reutilizado** | Dos firmas con el mismo `k` → se despeja `d` algebraicamente. **Recupera la clave privada.** (caso PS3 de Sony) |
| **Nonce `k` predecible/sesgado** | Lattice attacks (LLL) recuperan `d` con pocas firmas. |
| Curvas débiles / no estándar | Posible backdoor o ECDLP más fácil. |

> Por eso existe **EdDSA / Ed25519**: deriva `k` de forma determinista del mensaje + clave, eliminando el riesgo del nonce. Preferible a ECDSA cuando se puede elegir.

Curvas comunes: `secp256r1` (P-256, NIST), `secp256k1` (Bitcoin), `Curve25519` (Ed25519).

# Recursos
### [[!Digital Sign]] · [[PGP]]
### [tutorialspoint — ECDSA](https://www.tutorialspoint.com/cryptography/cryptography_ecdsa_algorithm.htm)
### [Wikipedia — ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)
