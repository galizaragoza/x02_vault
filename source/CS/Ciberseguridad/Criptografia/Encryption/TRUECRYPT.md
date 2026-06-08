#reference #Criptografia

**TrueCrypt** = software (descontinuado en 2014) de cifrado de disco *on-the-fly*. Su sucesor mantenido es **VeraCrypt**. Crea volúmenes cifrados que se montan como discos virtuales; el cifrado/descifrado es transparente en RAM.

> TrueCrypt está abandonado y con auditoría con fallos menores; **usa VeraCrypt** en nuevos sistemas. Esta nota cubre el formato porque sigue apareciendo en forense y CTF.

# Conceptos

| Concepto | Descripción |
|----------|-------------|
| **Volumen** | Fichero contenedor (o partición/disco entero) cifrado. |
| **On-the-fly** | Datos descifrados solo en memoria, nunca en disco. |
| **Cascada** | Encadena cifrados (p.ej. AES-Twofish-Serpent). |
| **Volumen oculto** | Volumen dentro del espacio libre de otro → **negación plausible**: dos contraseñas, una abre el señuelo, otra el oculto. |
| **Keyfiles** | Ficheros que actúan como factor adicional a la contraseña. |

Cifrados: AES, Serpent, Twofish (y cascadas). Hash de derivación: SHA-512, Whirlpool, RIPEMD-160.

# Uso (VeraCrypt CLI)

```bash
# Crear volumen
veracrypt --create vol.hc --size 100M --encryption AES --hash sha-512 \
          --filesystem FAT --volume-type normal

# Montar / desmontar
veracrypt --mount vol.hc /mnt/secret
veracrypt --dismount /mnt/secret
veracrypt --dismount-all
```

# En forense / CTF

- Cabecera del volumen cifrada; sin firma → difícil identificar (entropía alta, tamaño redondo, sin extensión clara).
- Crackear la contraseña: `truecrypt2john` / `veracrypt2john` + John/Hashcat (muy lento por el KDF iterado).
- La clave maestra puede estar en un **memory dump** (extraíble con Volatility / bulk_extractor).

```bash
veracrypt2john vol.hc > hash.txt
hashcat -m 13721 hash.txt rockyou.txt   # VeraCrypt AES (ajustar modo)
```

# Recursos
### [VeraCrypt — documentación](https://www.veracrypt.fr/en/Documentation.html)
### [TrueCrypt docs (archivo)](https://www.truecrypt.org/docs/)
