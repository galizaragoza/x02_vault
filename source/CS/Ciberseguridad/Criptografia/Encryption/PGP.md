#reference #Criptografia

**PGP** (Pretty Good Privacy) / **GPG** (GnuPG, su implementación libre) = cifrado y firma de datos por **criptografía híbrida**: cifrado asimétrico para intercambiar una clave de sesión simétrica que cifra el mensaje real.

# Modelo

| Pieza | Función |
|-------|---------|
| **Clave pública** | Se comparte; cifra mensajes hacia ti y verifica tus firmas. |
| **Clave privada** | Secreta; descifra lo que te envían y firma en tu nombre. |
| **Clave de sesión** | Simétrica (AES) de un solo uso; cifra el cuerpo. Se cifra con la pública del receptor. |
| **Web of Trust** | Modelo de confianza descentralizado: las claves se firman entre usuarios (sin CA central). |

> Confidencialidad = cifrar con la **pública del receptor**. Autenticidad = firmar con la **privada del emisor**.

# GPG — comandos esenciales

```bash
# Generar par de claves
gpg --full-generate-key

# Listar claves
gpg --list-keys              # públicas
gpg --list-secret-keys       # privadas

# Exportar / importar
gpg --armor --export tu@correo.com > pub.asc
gpg --import pub.asc
gpg --armor --export-secret-keys tu@correo.com > priv.asc   # ¡backup sensible!

# Cifrar un fichero para un destinatario
gpg --encrypt --recipient dest@correo.com fichero.txt   # -> fichero.txt.gpg

# Descifrar
gpg --decrypt fichero.txt.gpg > fichero.txt

# Firmar y verificar
gpg --sign doc.txt                  # firma + comprime
gpg --clearsign doc.txt             # firma legible (texto plano + firma)
gpg --detach-sign doc.txt           # firma separada (.sig)
gpg --verify doc.txt.sig doc.txt

# Cifrado simétrico (solo contraseña, sin claves)
gpg --symmetric fichero.txt

# Huella (verificar identidad de una clave)
gpg --fingerprint tu@correo.com
```

# En seguridad ofensiva / CTF

- Ficheros `.gpg` / `.asc` / `.pgp` en loot → buscar la **clave privada** y su passphrase.
- Claves privadas en backups, `.bashrc`, repos → crackear passphrase con `gpg2john` + John.
- `~/.gnupg/` contiene el keyring; revisar en post-explotación.

```bash
gpg2john priv.asc > hash.txt
john hash.txt --wordlist=rockyou.txt
```

# Recursos
### [[!Digital Sign]] · [[ECDSA]]
### [GnuPG — manual](https://www.gnupg.org/documentation/)
### [Wikipedia — PGP](https://en.wikipedia.org/wiki/Pretty_Good_Privacy)
