#reference #Ciberseguridad

**SET** (Social-Engineer Toolkit) = framework Python para ataques de **ingeniería social**: phishing, clonado de webs, payloads, USB malicioso. Estándar en awareness/red team. Solo para engagements autorizados.

# Lanzar

```bash
sudo setoolkit
```

Menú principal → **1) Social-Engineering Attacks**.

# Vectores principales

| Vector | Uso |
|--------|-----|
| **Website Attack Vectors** | Clonar una web legítima. |
| └ Credential Harvester | Clon que captura usuario/contraseña. |
| └ Site Cloner | Copia exacta de la página objetivo. |
| **Spear-Phishing** | Envío masivo de correos con adjunto/enlace malicioso. |
| **Infectious Media Generator** | Autorun/USB malicioso. |
| **Payload + Listener** | Generar payload (Meterpreter) + handler. |
| **QRCode Generator** | QR que apunta a URL maliciosa. |

# Credential Harvester (flujo típico)

```
1) Social-Engineering Attacks
2) Website Attack Vectors
3) Credential Harvester Attack Method
2) Site Cloner
-> IP del atacante (donde se sirve el clon)
-> URL a clonar (ej. https://login.victima.com)
# Las credenciales capturadas se muestran en consola / se guardan en report
```

# Notas

- Combinar con un dominio similar (typosquatting) y certificado TLS para credibilidad.
- La fase previa es OSINT del objetivo → ver [[OSINT_cheatsheet]].
- Config en `/etc/setoolkit/set.config`.

> Uso exclusivo en pruebas de phishing autorizadas / concienciación.

# Recursos
### [[OSINT_cheatsheet]] · [[Social Analyzer]]
### [SET — repo (TrustedSec)](https://github.com/trustedsec/social-engineer-toolkit)
