#reference #Criptografia

**MD5** (Message Digest 5) = función hash de 128 bits (32 hex) diseñada por Rivest (1991). **Criptográficamente rota**: las colisiones son triviales hoy. Solo válida como checksum no adversario (detectar corrupción accidental), nunca para seguridad.

# Propiedades

| Aspecto | Valor |
|---------|-------|
| Salida | 128 bits → 32 caracteres hexadecimales |
| Construcción | Merkle–Damgård |
| Estado | ❌ Roto (colisiones desde 2004) |

# Por qué está roto

- **Colisiones prácticas**: dos entradas distintas con el mismo MD5 se generan en segundos.
- **Chosen-prefix collisions** → certificados X.509 falsificados (ataque **Flame**, 2012).
- **No resiste preimagen para passwords**: extremadamente rápido → cracking masivo por GPU y rainbow tables.

> Nunca usar MD5 para firmas, certificados ni almacenamiento de contraseñas. Para integridad real usar [[!SHA]] (SHA-256); para passwords, Argon2/bcrypt.

# Comandos

```bash
md5sum fichero
echo -n "texto" | md5sum
md5sum -c checksums.txt        # verificar integridad
```

# Cracking

```bash
hashcat -m 0 hash.txt rockyou.txt          # MD5 plano
hashcat -m 10 hash.txt rockyou.txt         # md5($pass.$salt)
john --format=raw-md5 hash.txt --wordlist=rockyou.txt
```

MD5 aparece muchísimo en CTF y volcados de BBDD heredadas. Identificar: 32 hex → probablemente MD5 o NTLM (`hashid`).

# Recursos
### [[!SHA]] · [[!Digital Sign]]
### [Wikipedia — MD5](https://en.wikipedia.org/wiki/MD5)
### [Flame / chosen-prefix collision](https://en.wikipedia.org/wiki/Flame_(malware))
