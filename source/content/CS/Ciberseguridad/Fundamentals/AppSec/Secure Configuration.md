#reference #Ciberseguridad

**Secure Configuration** = endurecer la configuración de apps, servicios e infraestructura para reducir la superficie de ataque. Es uno de los controles base: el software por defecto suele venir **inseguro por comodidad**.

# Problemas frecuentes (OWASP A05 — Security Misconfiguration)

| Misconfig | Riesgo |
|-----------|--------|
| Credenciales/cuentas por defecto | Acceso trivial (admin/admin). |
| Servicios/puertos/funciones sin uso activos | Más superficie de ataque. |
| Mensajes de error verbosos / stack traces | Fuga de información. |
| Directory listing activo | Exposición de ficheros. |
| Cabeceras de seguridad ausentes | XSS, clickjacking, etc. |
| Permisos cloud (S3/IAM) demasiado abiertos | Exposición de datos. |
| Software sin parchear | Explotación de CVEs conocidos. |

# Buenas prácticas

- **Quitar lo que no se usa** (cuentas, servicios, módulos, features de ejemplo).
- Cambiar **todas** las credenciales por defecto.
- Errores genéricos al usuario, detalle solo en logs.
- Cabeceras: `Content-Security-Policy`, `HSTS`, `X-Content-Type-Options`, `X-Frame-Options`.
- Proceso repetible y **automatizado** (IaC, baselines CIS) → mismo hardening en todos los entornos.
- Revisar permisos cloud (mínimo privilegio en IAM/buckets).

# Relación

Es la cara aplicada del [[Hardening]] de sistemas y enlaza con [[!Security By Default]] (que viene seguro de fábrica) y la configuración segura como parte del SDLC.

# Recursos
### [[!Security By Default]] · [[Hardening]] · [[AppSec 101]]
### [OWASP A05 — Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)
### [Simple steps for attack surface reduction (THN)](https://thehackernews.com/2025/08/simple-steps-for-attack-surface.html)
