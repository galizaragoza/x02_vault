#reference #Ciberseguridad #OSINT

Motor de búsqueda de hosts y certificados en Internet. Escaneo continuo + base de datos histórica de **certificados X.509** (su punto fuerte frente a [[Shodan_CS]]). Sintaxis **Censys Search 2.0**.

> Resumen rápido en [[Dorks_CS]]. Esta nota es el cheatsheet completo (hosts + certs + CLI).

# Dos índices

| Índice | Qué busca | Pivote clave |
|--------|-----------|--------------|
| **Hosts** | IPs y servicios escaneados. | `services.*` |
| **Certificates** | Certificados X.509 vistos en CT logs + escaneos. | `names`, `parsed.*` |

> El índice de **certificados** permite mapear toda la infraestructura de un dominio (incl. subdominios y hosts no obvios) por su CN/SAN antes de tocar la red.

# Filtros — índice Hosts

## Servicios
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `services.port:` | Puerto. | `services.port:22` |
| `services.service_name:` | Protocolo (`HTTP`, `SSH`, `MYSQL`…). | `services.service_name:SSH` |
| `services.transport_protocol:` | TCP/UDP. | `services.transport_protocol:UDP` |
| `services.software.product:` | Producto del banner. | `services.software.product:"nginx"` |
| `services.software.vendor:` | Fabricante. | `services.software.vendor:"Apache"` |
| `services.banner:` | Texto del banner. | `services.banner:"SSH-2.0"` |

## HTTP
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `services.http.response.status_code:` | Código HTTP. | `...status_code:200` |
| `services.http.response.html_title:` | Título de página. | `...html_title:"Dashboard"` |
| `services.http.response.headers.server:` | Cabecera Server. | `...server:"nginx"` |
| `services.http.response.body:` | Cuerpo de la respuesta. | `...body:"password"` |
| `services.http.response.favicon.hashes:` | Hash del favicon. | `...favicon.hashes:"sha256:..."` |

## TLS / Certificado (en hosts)
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `services.tls.certificates.leaf_data.subject.common_name:` | CN. | `...common_name:"*.target.com"` |
| `services.tls.certificates.leaf_data.issuer.organization:` | Emisor. | `...issuer.organization:"Let's Encrypt"` |
| `services.tls.certificates.leaf_data.subject_dn:` | Subject DN completo. | — |

## Red y localización
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `ip:` | IP o CIDR. | `ip:8.8.8.0/24` |
| `autonomous_system.asn:` | ASN. | `autonomous_system.asn:15169` |
| `autonomous_system.name:` | Nombre del AS. | `autonomous_system.name:"GOOGLE"` |
| `location.country:` | País. | `location.country:"Spain"` |
| `location.country_code:` | Código ISO. | `location.country_code:ES` |
| `dns.reverse_dns.names:` | rDNS. | `dns.reverse_dns.names:"*.target.com"` |
| `labels:` | Etiquetas (`login-page`, `iot`, `c2`, `remote-access`…). | `labels:c2` |

# Filtros — índice Certificates

| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `names:` | CN o cualquier SAN. | `names:target.com` |
| `parsed.subject.common_name:` | CN exacto. | `parsed.subject.common_name:"*.target.com"` |
| `parsed.issuer.organization:` | Emisor. | `parsed.issuer.organization:"DigiCert"` |
| `parsed.validity.start:` | Inicio de validez. | `parsed.validity.start:[2026-01-01 TO *]` |
| `parsed.fingerprint_sha256:` | Huella del cert. | — |
| `parsed.extensions.subject_alt_name.dns_names:` | SANs. | `...dns_names:dev.target.com` |

# Lógica y rangos

| Operador | Función | Ejemplo |
|----------|---------|---------|
| `and` / `or` / `not` | Booleanos. | `services.port:443 and location.country_code:ES` |
| `(...)` | Agrupación. | `(services.port:80 or services.port:443)` |
| `[a TO b]` | Rango inclusivo. | `services.port:[8000 TO 9000]` |
| `{a TO b}` | Rango exclusivo. | `parsed.validity.start:{* TO 2020-01-01}` |
| `*` | Comodín / existencia. | `services.software.product:nginx*` |
| `:` con `*` | El campo existe. | `services.jarm.fingerprint:*` |

# CLI de Censys

```bash
pip install censys
censys config                   # guardar API ID + Secret

# buscar hosts
censys search 'services.service_name:SSH and location.country_code:ES' --index-type hosts

# ver un host
censys view 8.8.8.8

# buscar certificados
censys search 'names:target.com' --index-type certs

# agregaciones (report/facets)
censys account                  # cuota de la API
```

# Recetas listas para usar

| Objetivo | Query | Índice |
|----------|-------|--------|
| Subdominios por certificado | `names:target.com` | certs |
| Superficie de un dominio | `services.tls.certificates.leaf_data.subject.common_name:"*.target.com"` | hosts |
| Paneles de login | `labels:login-page and services.port:443` | hosts |
| RDP expuesto | `services.port:3389` | hosts |
| Bases de datos abiertas | `labels:database and not services.tls.version_selected:*` | hosts |
| C2 conocidos | `labels:c2` | hosts |
| Por ASN y país | `autonomous_system.asn:15169 and location.country_code:US` | hosts |
| Certs caducando pronto | `parsed.validity.end:[* TO 2026-09-01] and names:target.com` | certs |
| Pivotar por favicon | `services.http.response.favicon.hashes:"sha256:..."` | hosts |
| JARM fingerprint (TLS stack) | `services.jarm.fingerprint:"<hash>"` | hosts |

> **Flujo OSINT típico:** `names:target.com` (certs) → extraer SANs/subdominios → cruzar con el índice de hosts por CN para localizar IPs reales detrás de CDN.

# Recursos
### [[Dorks_CS]] · [[Shodan_CS]] · [[RECON_CS]] · [[DNS_CS]]
### [Censys Search 2.0 — sintaxis](https://search.censys.io/search/language)
### [Censys — definiciones de campos (hosts)](https://search.censys.io/search/definitions?resource=hosts)
### [Awesome Censys Queries](https://github.com/thehappydinoa/awesome-censys-queries)
### [Censys CLI / Python](https://censys-python.readthedocs.io/)
