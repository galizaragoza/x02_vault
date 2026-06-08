#reference #Ciberseguridad

**AppSec** (Application Security) = disciplina de construir, probar y mantener aplicaciones seguras a lo largo de todo el ciclo de vida (SDLC). Objetivo: prevenir, detectar y corregir vulnerabilidades en el software.

# Seguridad en el SDLC (SSDLC / DevSecOps)

| Fase | Control |
|------|---------|
| Diseño | Threat modeling, *security by design* → [[!Security By Default]]. |
| Codificación | Estándares de código seguro, revisión. |
| Build | **SAST**, análisis de dependencias (SCA). |
| Test | **DAST**, [[Fuzzing]], pentest. |
| Despliegue | [[Secure Configuration]], secrets management. |
| Operación | Monitorización, [[Logging 101]], parches. |

# Técnicas de seguridad (defensa del software)

- **Normalización** de entradas (canonicalización).
- **Stored procedures** / consultas parametrizadas (anti [[SQL_Cheatsheet|SQLi]]).
- **Ofuscación y camuflaje** (dificultar reversing — defensa en profundidad, no sustituto).
- **Reutilización de código** seguro / librerías mantenidas.
- **SDKs** confiables y actualizados.
- **Código firmado** (integridad y origen).
- **Cookies de seguridad** (`HttpOnly`, `Secure`, `SameSite`).

# Comprobaciones de integridad

- **Checksum** y **[[!SHA|hashing]]** para verificar que el código/datos no se alteran.
- **Control de versiones** y trazabilidad.
- **Copias de seguridad**.
- **[[Kerberos|AA&A]]** — autenticación, autorización y auditoría.

# Referencias clave

- **OWASP Top 10** — riesgos web más críticos.
- **OWASP ASVS** — estándar de verificación.
- **CWE / CVE** — debilidades y vulnerabilidades conocidas.

# Recursos
### [[Secure Configuration]] · [[!Security By Default]] · [[Fuzzing]]
### [OWASP Top 10](https://owasp.org/Top10/) · [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
