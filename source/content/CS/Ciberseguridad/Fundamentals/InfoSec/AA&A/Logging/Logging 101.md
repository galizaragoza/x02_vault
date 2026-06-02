#reference #Ciberseguridad

**Logging** = registrar eventos de un sistema para auditoría, detección de incidentes, forense y cumplimiento. Es la "A" final de **AAA** (Accounting/Auditing): saber **quién hizo qué y cuándo**.

# Logging Lifecycle

1. **Generating** — la fuente produce el evento.
2. **Transmitting** — envío al colector (syslog, agente).
3. **Storing** — almacenamiento (íntegro y retenido).
4. **Analyzing** — correlación y alertas (SIEM).
5. **Disposing** — retención y borrado según política.

![[logs-lifecycle.png|467x377]]

# Qué registrar

Autenticación (éxitos/fallos), cambios de privilegios, accesos a datos sensibles, cambios de config, errores, conexiones de red, actividad administrativa.

# Buenas prácticas

| Práctica | Por qué |
|----------|---------|
| **Centralizar** | Un host comprometido no puede borrar sus logs (syslog remoto/SIEM). |
| **Integridad** | Append-only, firmado/hash → detectar manipulación. |
| **Sincronizar reloj (NTP)** | Correlación fiable entre fuentes. |
| **Retención** | Cumplir normativa (GDPR, PCI-DSS). |
| **No registrar secretos** | Evitar contraseñas/PII en claro en los logs. |
| **Alertas** | Detección en tiempo real, no solo post-mortem. |

# Herramientas

| Capa | Ejemplos |
|------|----------|
| Generación | `journald`, `syslog`, Windows Event Log, auditd |
| Transporte | rsyslog, syslog-ng, Fluentd, Filebeat |
| SIEM / análisis | Splunk, ELK/Elastic, Wazuh, Graylog |

> Para el atacante, los logs son lo primero que se manipula/borra (anti-forense). Por eso centralizar + integridad es crítico. Relacionado: [[Rootkit]] oculta actividad.

# Recursos
### [[siem]] · [[Hardening]]
### [OWASP — Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html)
