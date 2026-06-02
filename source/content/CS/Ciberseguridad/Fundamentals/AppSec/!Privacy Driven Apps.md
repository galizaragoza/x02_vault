#reference #Ciberseguridad

**Privacy by Design** = construir apps con la **privacidad como principio, medio y objetivo** desde el inicio, no como parche posterior. Tratar los datos personales como un pasivo: recoger el mínimo, protegerlos por defecto y dar control al usuario.

# Qué es y cómo implementarlo en el SDLC

Integrar privacidad en cada fase: diseño (DPIA / evaluación de impacto), recogida mínima, almacenamiento cifrado, retención limitada, borrado. Complementa [[!Security By Default]] (privacidad por defecto).

# Principios (Privacy by Design — Cavoukian)

| Principio | Idea |
|-----------|------|
| Proactivo, no reactivo | Prevenir, no remediar. |
| Privacidad **por defecto** | Máxima protección sin acción del usuario. |
| Privacidad **integrada** en el diseño | Parte de la arquitectura. |
| Funcionalidad total | Privacidad **y** usabilidad (no trade-off). |
| Seguridad de extremo a extremo | Todo el ciclo de vida del dato. |
| Visibilidad y transparencia | El usuario sabe qué se hace con sus datos. |
| Respeto al usuario | Control y consentimiento. |

# Necesidad y casos reales

La privacidad es exigencia legal (**GDPR**, CCPA) y de confianza. Brechas de datos = multas + pérdida de reputación. Casos: filtraciones masivas por exceso de datos recogidos, tracking sin consentimiento.

# Técnicas

| Técnica | Uso |
|---------|-----|
| **Minimización de datos** | Recoger solo lo necesario. |
| **Anonimización / seudonimización** | Romper la asociación con la persona. |
| **Cifrado at-rest e in-transit** | Proteger los datos. |
| **Differential privacy** | Estadística sin exponer individuos. |
| **Gestión de consentimiento** | Control granular del usuario. |
| **Data retention / deletion** | Borrar lo que ya no se necesita. |

# Formación

Entrenar a los equipos (dev, producto, soporte) en privacidad: qué es dato personal, minimización, manejo de incidentes, **DevSecOps** integrando privacidad en el pipeline.

# Recursos
### [[!Security By Default]] · [[AppSec 101]]
### [GDPR — Data protection by design](https://gdpr-info.eu/art-25-gdpr/) · [DevSecOps en el SDLC (Help Net)](https://www.helpnetsecurity.com/2025/09/03/devsecops-in-sdlc-secure-development-teams-video/)
