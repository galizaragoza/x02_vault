#reference #Networking

**Domain Shadowing** = el atacante roba credenciales de cuentas de **registradores/gestores de DNS** y crea **subdominios maliciosos** bajo dominios legítimos, sin que el dueño lo note. Hereda la buena reputación del dominio padre → evade blocklists.

# Cómo

```
1. Comprometer la cuenta del registrador (phishing, credential stuffing)
2. Crear subdominios nuevos: pago.empresa-legitima.com -> IP del atacante
3. El dominio padre sigue funcionando normal -> el dueño no sospecha
4. Usar los subdominios para phishing, malware o C2
```

# Por qué es eficaz

| Ventaja para el atacante | Motivo |
|--------------------------|--------|
| Reputación heredada | El dominio padre está en allowlists. |
| Sigilo | El dueño rara vez audita todos sus subdominios. |
| Validez TLS | Puede emitir certs para los subdominios. |
| Difícil de bloquear | Bloquear el padre afecta al negocio legítimo. |

> Se diferencia del **subdomain takeover** (reclamar un CNAME huérfano a un servicio no provisionado): aquí el atacante **controla la cuenta DNS** y crea registros nuevos.

# Detección / mitigación

- **MFA** y contraseñas fuertes en la cuenta del registrador.
- Monitorizar **Certificate Transparency** (crt.sh) para subdominios nuevos no autorizados.
- Inventario y auditoría periódica de registros DNS.
- Alertas ante creación masiva de subdominios.

# Recursos
### [[DNS_CS]] · [[Fast Flux]] · [[Dorks_CS]]
### [Cisco Talos — Domain Shadowing](https://blog.talosintelligence.com/)
