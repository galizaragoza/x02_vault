#reference #Networking

**Fast Flux** = técnica de sigilo para ocultar infraestructura maliciosa (C2, phishing, malware) **rotando rápidamente las IPs** asociadas a un dominio. Cada pocos minutos cambian los registros A (TTL muy bajo), apoyándose en una botnet de hosts comprometidos que actúan de proxy hacia el servidor real.

# Variantes

| Variante | Qué rota |
|----------|----------|
| **Single Flux** | Solo las IPs (registros A) del dominio. |
| **Double Flux** | Las IPs **y** los servidores DNS autoritativos (registros NS) → mucho más difícil rastrear el origen. |

# Indicadores

- **TTL muy bajo** (segundos/minutos) en los registros A.
- Muchas IPs distintas para un dominio en poco tiempo, en **ASNs/países dispersos** (residenciales).
- Las IPs corresponden a hosts comprometidos (no datacenters).
- Reverse DNS inconsistente.

```bash
# Observar la rotación
watch -n 30 'dig +short malicioso.com A'
dig malicioso.com A    # mirar el TTL
```

# Por qué se usa

Resiliencia: aunque se tumbe una IP, el dominio sigue vivo. Evade blocklists basadas en IP y dificulta el takedown. La defensa efectiva es bloquear/sinkhole el **dominio**, no las IPs.

# Mitigación

- Bloqueo a nivel de dominio (DNS RPZ, threat intel).
- Detección por TTL bajo + alta cardinalidad de IPs.
- Cooperación con registradores para takedown del dominio.

# Recursos
### [[DNS_CS]] · [[Domain Shadowing]] · [[DNS Tunneling]]
### [Wikipedia — Fast flux](https://en.wikipedia.org/wiki/Fast_flux)
