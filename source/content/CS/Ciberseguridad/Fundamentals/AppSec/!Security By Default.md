#reference #Ciberseguridad

**Security by Default** = principio de diseño según el cual un sistema debe venir **seguro de fábrica**, sin requerir que el usuario active protecciones. La opción más segura es la **predeterminada**; relajarla es una decisión consciente.

# Idea

> La seguridad no debe depender de que el usuario "se acuerde" de configurarla. Por defecto = mínimo privilegio + mínima exposición.

| Por defecto debería ser… | En vez de… |
|--------------------------|------------|
| Todo denegado (allowlist) | Todo permitido (blocklist). |
| Funciones peligrosas **desactivadas** | Activadas "por comodidad". |
| Sin cuentas/contraseñas por defecto | admin/admin. |
| Cifrado activado (TLS, at-rest) | Texto plano opcional. |
| Permisos mínimos | Acceso amplio. |

# Relación con otros principios

| Principio | Diferencia |
|-----------|-----------|
| **Security by Design** | La seguridad se piensa desde la arquitectura. |
| **Security by Default** | Los valores **predeterminados** son los seguros. |
| **Secure Configuration** | El acto de endurecer lo que vino inseguro → [[Secure Configuration]]. |
| **Fail securely** | Ante error, el sistema cae a un estado seguro (denegar). |

Forma parte de los principios de *Privacy & Security by Design* (también recogidos en GDPR).

# Recursos
### [[Secure Configuration]] · [[!Privacy Driven Apps]] · [[AppSec 101]]
### [OWASP — Secure by Default](https://owasp.org/www-project-proactive-controls/) · [Attack surface reduction (THN)](https://thehackernews.com/2025/08/simple-steps-for-attack-surface.html)
