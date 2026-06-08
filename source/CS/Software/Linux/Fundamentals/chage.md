`chage` (change age) gestiona la información de caducidad y *aging* de las contraseñas y cuentas de usuario almacenada en `/etc/shadow`. Permite forzar cambios periódicos de contraseña, definir avisos previos, periodos de inactividad y fechas de expiración de la cuenta. Sin opciones de modificación entra en modo interactivo preguntando campo por campo. Las opciones de escritura requieren root; un usuario sin privilegios solo puede consultar su propia información con `-l`.

```
chage [opciones] LOGIN
```

> Cada parámetro mapea a un campo de `/etc/shadow`. Las fechas se cuentan en días desde la *epoch* (1970-01-01). El valor `-1` desactiva la política asociada; `0` en *lastday* fuerza el cambio en el próximo login.

---

## Consulta

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` / `--list` | Muestra toda la información de aging de la cuenta en formato legible. | `chage -l juan` |

---

## Política de contraseña

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-m` / `--mindays DÍAS` | Días mínimos entre cambios de contraseña. `0` permite cambiarla siempre. | `chage -m 1 juan` |
| `-M` / `--maxdays DÍAS` | Días máximos de validez antes de exigir cambio. `-1` desactiva la caducidad. | `chage -M 90 juan` |
| `-W` / `--warndays DÍAS` | Días de aviso antes de que caduque la contraseña. | `chage -W 7 juan` |
| `-d` / `--lastday FECHA` | Fecha (o nº de días desde epoch) del último cambio. `0` fuerza cambio en el próximo login. | `chage -d 0 juan` |

---

## Inactividad y expiración de cuenta

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-I` / `--inactive DÍAS` | Días tras caducar la contraseña antes de bloquear la cuenta. `-1` desactiva. | `chage -I 14 juan` |
| `-E` / `--expiredate FECHA` | Fecha de expiración de la cuenta (YYYY-MM-DD o días desde epoch). `-1` la deja sin expiración. | `chage -E 2026-12-31 juan` |

---

## Operación sobre raíz alternativa y ayuda

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-R` / `--root DIR` | Aplica los cambios sobre un árbol chroot. | `chage -R /mnt/sys -M 60 juan` |
| `-i` / `--iso8601` | (Donde esté disponible) muestra/interpreta fechas en formato ISO 8601. | `chage -l -i juan` |
| `-h` / `--help` | Ayuda. | `chage -h` |

---

## Casos de uso comunes

```bash
# Ver la política de aging completa de un usuario
chage -l juan

# Forzar cambio de contraseña en el próximo inicio de sesión
chage -d 0 juan

# Política típica: mín 1 día, máx 90, aviso 7, bloqueo a los 14 de caducar
chage -m 1 -M 90 -W 7 -I 14 juan

# Cuenta temporal que expira en una fecha fija
chage -E 2026-12-31 contractor

# Desactivar toda caducidad de contraseña (cuenta de servicio)
chage -M -1 -I -1 -E -1 svc_app

# Modo interactivo (pregunta campo por campo)
chage juan
```

Relacionado: [[usuarios-y-grupos]], [[usermod]], [[Hardening]].
