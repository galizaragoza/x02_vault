#reference #Ciberseguridad

**Credential stuffing** = ataque automatizado contra paneles de login que prueba **credenciales filtradas** (usuario:contraseña de brechas previas, compradas/obtenidas en la darknet). Explota la **reutilización de contraseñas**: si un usuario repite la misma en varios sitios, una filtración compromete todas.

# Cómo

```
1. Obtener combolists (brechas: combos email:pass)
2. Automatizar intentos contra el login objetivo (con proxies/rotación)
3. Las cuentas que validan -> account takeover (ATO)
```

> Distinto de **brute force** (probar muchas contraseñas contra un usuario) o **password spraying** (una contraseña común contra muchos usuarios). Aquí se usan combos reales ya filtrados → tasa de acierto mayor.

| Ataque | Estrategia |
|--------|-----------|
| Brute force | Muchas pass contra 1 usuario. |
| Password spraying | 1 pass común contra muchos usuarios (evita bloqueos). |
| **Credential stuffing** | Combos reales filtrados contra el login. |

# Herramientas

OpenBullet, Sentry MBA, Hydra/scripts. Combolists de fuentes como HaveIBeenPwned (defensivo), foros/darknet (ofensivo).

# Mitigación

| Defensa | Efecto |
|---------|--------|
| **MFA** | Aunque la pass sea válida, falta el segundo factor. |
| Detección de combos filtrados | Bloquear contraseñas vistas en brechas (HIBP API). |
| Rate limiting + CAPTCHA | Frenar la automatización. |
| Detección de anomalías | IPs/dispositivos inusuales, velocidad de intentos. |
| Bloqueo/alerta por intentos | Sin facilitar enumeración de usuarios. |

# Recursos
### [[!SHA]] · [[KeePass]]
### [OWASP — Credential stuffing](https://owasp.org/www-community/attacks/Credential_stuffing) · [Have I Been Pwned](https://haveibeenpwned.com/)
