
|**Registro**|**Nombre Completo**|**Función Principal**|**Ejemplo de Contenido**|
|---|---|---|---|
|**A**|Address|Apunta un nombre de dominio a una **dirección IPv4**.|`google.com -> 142.250.184.206`|
|**AAAA**|IPv6 Address|Apunta un nombre de dominio a una **dirección IPv6**.|`google.com -> 2a00:1450:4003:80b::200e`|
|**CNAME**|Canonical Name|Crea un alias. Apunta un nombre a **otro nombre** de dominio.|`www.ejemplo.com -> ejemplo.com`|
|**MX**|Mail Exchange|Indica a qué servidores debe enviarse el **correo electrónico**.|`10 mail.google.com`|
|**TXT**|Text Record|Almacena información de texto (usado para verificación y seguridad SPF/DKIM).|`v=spf1 include:_spf.google.com ~all`|
|**NS**|Name Server|Indica qué servidores son los **autoritativos** para el dominio.|`ns1.digitalocean.com`|
|**SOA**|Start of Authority|Contiene info administrativa sobre la zona (email del admin, tiempos de refresco).|`ns1.server.com. admin.server.com.`|
|**PTR**|Pointer Record|El inverso del registro A. Mapea una **IP a un nombre**.|`8.8.8.8 -> dns.google`|
|**SRV**|Service Record|Define la ubicación (puerto y hostname) de **servicios específicos**.|`_sip._tcp.ejemplo.com`|
|**CAA**|Certification Authority Authorization|Indica qué CAs pueden emitir certificados para el dominio.|`0 issue "letsencrypt.org"`|
|**DNSKEY / DS / RRSIG**|DNSSEC|Claves y firmas para autenticar respuestas DNS.|—|

# Jerarquía y resolución

```
. (root)  →  TLD (.com)  →  autoritativo (ejemplo.com)  →  host (www)
```

| Tipo de servidor | Función |
|------------------|---------|
| **Recursivo (resolver)** | Resuelve en nombre del cliente; cachea. Ej. `8.8.8.8`, `1.1.1.1`. |
| **Root** | 13 conjuntos (`a–m.root-servers.net`); apuntan a los TLD. |
| **TLD** | Autoritativos de `.com`, `.org`, `.es`… |
| **Autoritativo** | Tiene la zona real del dominio (registros A, MX…). |

> Una consulta típica es **recursiva** del cliente al resolver, e **iterativa** del resolver hacia root → TLD → autoritativo.

# Puertos y transporte

| Puerto | Uso |
|--------|-----|
| `53/UDP` | Consultas normales (rápidas, sin estado). |
| `53/TCP` | Respuestas grandes (>512B), AXFR (zone transfer), fallback. |
| `853/TCP` | DNS over TLS (DoT). |
| `443/TCP` | DNS over HTTPS (DoH). |

# Herramientas de consulta

```sh
## dig — la navaja suiza del DNS
dig ejemplo.com A +short          # solo el dato
dig ejemplo.com MX                # registros de correo
dig @1.1.1.1 ejemplo.com ANY      # contra un resolver concreto
dig ejemplo.com +trace            # resolución iterativa paso a paso
dig -x 8.8.8.8                    # PTR (DNS inverso)

## nslookup (interactivo / Windows)
nslookup -type=NS ejemplo.com

## host (rápido)
host -t TXT ejemplo.com
```

# Zone Transfer (AXFR)

Si un servidor autoritativo permite transferencia de zona a cualquiera, expone **todos** los registros (mina de oro en recon).

```sh
dig AXFR ejemplo.com @ns1.ejemplo.com
## Equivalente con host
host -l ejemplo.com ns1.ejemplo.com
```

# DNS en recon / seguridad

| Técnica | Herramienta / fuente |
|---------|----------------------|
| Enumeración de subdominios | `subfinder`, `amass`, crt.sh, [[Dorks_CS]] |
| Bruteforce / resolución | `puredns`, `dnsx`, `massdns` |
| Registros y mapeo | `dnsrecon`, `dnsenum`, [[dig]] |
| DNS inverso (PTR) | `dig -x`, barrido de rangos |
| Subdomain takeover | CNAME a servicio no reclamado (`can-i-take-over-xyz`) |

> Ataques clásicos: **cache poisoning** (envenenar caché del resolver), **DNS tunneling** (exfiltración por TXT/subdominios), **DNS spoofing**. DNSSEC mitiga la falsificación firmando las respuestas.

# Recursos
### [[IP_CS]] · [[Dorks_CS]] · [[RECON_CS]]
### [RFC 1035 — Domain Names](https://www.rfc-editor.org/rfc/rfc1035)
### [dig man / DNS lookup](https://linux.die.net/man/1/dig)
### [DNSDumpster](https://dnsdumpster.com/) · [crt.sh](https://crt.sh/)