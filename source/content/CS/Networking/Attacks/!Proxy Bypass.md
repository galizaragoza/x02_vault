#reference #Networking #Web_Security

**Proxy / filter bypass** = técnicas para eludir proxies, WAFs, filtros de URL o controles de acceso basados en red, llegando a recursos restringidos o saltándose inspección.

# Contextos

| Contexto | Bypass típico |
|----------|---------------|
| **WAF / filtro web** | Ofuscación de payload, encoding, case mixing, [[Parameter Pollution]]. |
| **Filtro de salida (egress)** | Túnel por protocolos permitidos: [[DNS Tunneling]], HTTP, ICMP, [[Tor]]. |
| **Allowlist por IP** | [[!IP spoofing]], cabeceras `X-Forwarded-For`, SSRF a recurso interno. |
| **Proxy corporativo** | `CONNECT` a puertos no estándar, domain fronting, DoH. |
| **SSRF / acceso interno** | Bypass de filtros de URL para alcanzar `127.0.0.1`/metadata. |

# Bypass de filtros de URL (SSRF / open redirect)

```
http://127.0.0.1        ->  http://localhost
                            http://0.0.0.0
                            http://0177.0.0.1      (octal)
                            http://2130706433       (decimal)
                            http://[::1]            (IPv6)
                            http://127.0.0.1.nip.io
http://interno@evil.com/    (confusión de userinfo)
http://evil.com#@interno/   (fragmento)
```

# Cabeceras para saltar controles por IP

```
X-Forwarded-For: 127.0.0.1
X-Real-IP: 127.0.0.1
X-Originating-IP: 127.0.0.1
X-Client-IP: 127.0.0.1
```

# Egress / exfiltración cuando todo está filtrado

- **DNS** casi siempre sale → [[DNS Tunneling]].
- **HTTP(S)** vía proxy → C2 con domain fronting / DoH.
- **ICMP** tunneling si se permite ping.
- **Tor / pluggable transports** para evadir DPI → [[Tor]].

# Mitigación

Filtrado por allowlist estricta (no blocklist), normalizar/validar URLs, resolver y comprobar la IP final (anti-SSRF), inspección TLS, no confiar en cabeceras `X-Forwarded-*` de clientes.

# Recursos
### [[Parameter Pollution]] · [[DNS Tunneling]] · [[!IP spoofing]] · [[Tor]]
### [PayloadsAllTheThings — SSRF / bypass](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery)
