#reference #Networking

**DNS Cache Poisoning** (DNS spoofing) = inyectar registros DNS falsos en la caché de un **resolver** para que devuelva una IP maliciosa. Las víctimas que consultan ese resolver son redirigidas a sitios del atacante (phishing, malware, MITM) aunque tecleen el dominio correcto.

# Cómo

Cuando un resolver no tiene la respuesta cacheada, consulta a servidores autoritativos. Si el atacante **adelanta** una respuesta falsa que el resolver acepta, esta queda **cacheada** y se sirve a todos hasta que expire el TTL.

```
1. Atacante fuerza al resolver a consultar dominio.com
2. Inunda con respuestas falsas (adivinando el Transaction ID + puerto)
3. La respuesta falsa llega antes que la legítima -> se cachea
4. Todas las víctimas van a la IP del atacante
```

> En un escenario de **Open Resolver** (resolver accesible desde Internet), el atacante puede envenenarlo directamente, redirigiendo a toda la organización.

# Ataque Kaminsky (2008)

Forzar consultas de subdominios inexistentes y spoofear respuestas con registros de delegación → envenenar el dominio entero, no solo un host. Mitigado con **aleatorización del puerto de origen** + Transaction ID de 16 bits.

# Mitigación

| Defensa | Efecto |
|---------|--------|
| **DNSSEC** | Firma las respuestas → el resolver detecta falsificaciones. |
| Aleatorizar TXID + puerto origen | Eleva muchísimo el coste de adivinar. |
| 0x20 encoding | Aleatoriza mayúsculas/minúsculas del nombre. |
| No exponer Open Resolvers | Resolver solo para clientes internos. |
| DoT / DoH | Cifra el canal cliente↔resolver. |

# Recursos
### [[DNS_CS]] · [[DNS Tunneling]] · [[Domain Shadowing]]
### [Wikipedia — DNS spoofing](https://en.wikipedia.org/wiki/DNS_spoofing) · [Kaminsky attack](https://en.wikipedia.org/wiki/Dan_Kaminsky)
