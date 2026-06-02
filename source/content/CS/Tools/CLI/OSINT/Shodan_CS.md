#reference #Ciberseguridad #OSINT

Motor de búsqueda de dispositivos conectados a Internet. Indexa **banners** de servicios (no contenido web como Google). Útil en recon pasivo, attack surface mapping y caza de activos expuestos.

> Resumen rápido en [[Dorks_CS]]. Esta nota es el cheatsheet completo (web + CLI + facets).

# Filtros de búsqueda (web + API)

## Red e identidad
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `ip:` | IP concreta. | `ip:8.8.8.8` |
| `net:` | Rango CIDR. | `net:216.58.192.0/19` |
| `asn:` | Sistema autónomo. | `asn:AS15169` |
| `hostname:` | Host/dominio en el banner o rDNS. | `hostname:target.com` |
| `org:` | Organización propietaria. | `org:"Microsoft"` |
| `isp:` | Proveedor. | `isp:"Verizon"` |
| `domain:` | Dominio (incluye subdominios). | `domain:target.com` |

## Servicio y software
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `port:` | Puerto TCP/UDP. | `port:445,139` |
| `product:` | Software/servidor del banner. | `product:nginx` |
| `version:` | Versión del producto. | `version:"1.14.0"` |
| `os:` | Sistema operativo. | `os:"Windows Server 2016"` |
| `tag:` | Etiqueta de Shodan (`cloud`, `ics`, `vpn`, `database`…). | `tag:database` |
| `hash:` | Hash del banner (pivotar a hosts idénticos). | `hash:-1957161625` |
| `has_screenshot:` | Solo hosts con captura. | `has_screenshot:true` |
| `has_vuln:` | Hosts con CVE conocido (requiere membership). | `has_vuln:true` |

## HTTP
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `http.title:` | Texto del `<title>`. | `http.title:"Dashboard"` |
| `http.status:` | Código de respuesta. | `http.status:401` |
| `http.html:` | Texto en el HTML. | `http.html:"password"` |
| `http.html_hash:` | Hash del HTML (pivotar a clones). | `http.html_hash:-1957161625` |
| `http.favicon.hash:` | **MMH3** del favicon → fingerprint de stack. | `http.favicon.hash:-247388890` |
| `http.component:` | Tecnología web detectada. | `http.component:"React"` |
| `http.waf:` | WAF detectado. | `http.waf:cloudflare` |

## Certificados / TLS
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `ssl:` | Texto en el certificado. | `ssl:"target.com"` |
| `ssl.cert.subject.cn:` | Common Name. | `ssl.cert.subject.cn:"*.gov"` |
| `ssl.cert.issuer.cn:` | Emisor. | `ssl.cert.issuer.cn:"Let's Encrypt"` |
| `ssl.cert.serial:` | Serial. | `ssl.cert.serial:12345` |
| `ssl.cert.expired:` | Certificado caducado. | `ssl.cert.expired:true` |
| `ssl.version:` | Versión TLS/SSL. | `ssl.version:tlsv1` |
| `ssl.cipher:` | Cifrado negociado. | `ssl.cipher:"TLS_RSA"` |

## Geolocalización y tiempo
| Filtro | Función | Ejemplo |
|--------|---------|---------|
| `country:` | Código ISO. | `country:ES` |
| `city:` | Ciudad. | `city:"Barcelona"` |
| `geo:` | Coordenadas (lat,lon[,radio km]). | `geo:41.38,2.17,5` |
| `before:` / `after:` | Fecha del banner (`dd/mm/yyyy`). | `after:01/01/2026` |

## Lógica
| Operador | Función | Ejemplo |
|----------|---------|---------|
| `(espacio)` | AND. | `port:21 product:proftpd` |
| `OR` | Unión. | `port:80 OR port:443` |
| `-` | Exclusión (NOT). | `port:80 -product:nginx` |

# CLI de Shodan

```bash
pip install shodan
shodan init <API_KEY>           # guardar clave

shodan myip                     # tu IP pública
shodan host 8.8.8.8             # toda la info de un host
shodan count 'apache country:ES'    # nº de resultados (no gasta créditos)
shodan search --fields ip_str,port,org 'product:MongoDB'   # buscar
shodan download datos 'port:3389 country:ES'   # volcar a datos.json.gz
shodan parse --fields ip_str,port datos.json.gz   # parsear el volcado

shodan stats --facets country 'product:nginx'   # top países
shodan stats --facets port 'org:"Telefonica"'   # puertos más comunes
shodan domain target.com        # subdominios + registros DNS
shodan scan submit 1.2.3.4      # escaneo on-demand (consume créditos)
shodan alert create "monitor" 1.2.3.0/24   # monitorización continua
shodan honeyscore 1.2.3.4       # probabilidad de honeypot (0-1)
```

# Facets (agregaciones)

Resumen estadístico sin descargar resultados. Vía web: pestaña **"Explore"/Facet Analysis**; vía CLI: `--facets`.

```bash
shodan stats --facets port,org,country 'ssl:"target.com"'
```

Facets útiles: `port`, `org`, `asn`, `country`, `os`, `product`, `http.component`, `ssl.version`, `vuln`.

# Recetas listas para usar

| Objetivo | Dork |
|----------|------|
| RDP expuesto | `port:3389` |
| Redis sin auth | `product:Redis port:6379 -authentication` |
| MongoDB abierto | `product:MongoDB port:27017` |
| Elasticsearch expuesto | `port:9200 product:Elastic` |
| Kibana sin auth | `http.title:"Kibana" http.status:200` |
| Cámaras IP | `webcamxp` · `has_screenshot:true tag:webcam` |
| ICS/SCADA | `port:502` (Modbus) · `port:102` (Siemens S7) · `tag:ics` |
| Paneles Jenkins | `http.title:"Dashboard [Jenkins]"` |
| GitLab expuesto | `http.title:"GitLab"` |
| Por favicon (stack) | `http.favicon.hash:-247388890` |
| Superficie de un dominio | `ssl:"target.com" 200` |
| Activos de una org en un país | `org:"Telefonica" country:ES port:445` |
| Certificados caducados | `ssl.cert.expired:true org:"target"` |

> El **favicon hash** (MMH3) es de los pivotes más potentes: calcúlalo de un favicon conocido y encuentra toda la infra que comparte el mismo front (incluso tras Cloudflare).

# Recursos
### [[Dorks_CS]] · [[Censys_CS]] · [[RECON_CS]] · [[OSINT_cheatsheet]]
### [Shodan — filtros oficiales](https://www.shodan.io/search/filters)
### [Shodan CLI docs](https://cli.shodan.io/)
### [nullfuzz — shodan-dorks](https://github.com/nullfuzz-pentest/shodan-dorks)
### [Awesome Shodan Queries](https://github.com/jakejarvis/awesome-shodan-queries)
