# Recon
## DNS
| Subdomain | Root Domain | TLD  | Name         |
| --------- | ----------- | ---- | ------------ |
| app       | .example    | .com | Example, Inc |
### Identificar subdominios en scope
- Wayback machine
- Censys
- Virustotal
- crt.sh
- Shodan
- **subfinder**

> [!important] Si el target es un wildcard, queremos encontrar también otros root domains que puedan no estar listados en el scope

```sh
subfinder [opciones] [dominio] -o output
## Obtener subdominios y guardarlos en una lista

cat output | puredns resolver | anew resolved
```

### DNS Fuzzing
Probar con subdomains conocidos puede revelear subdomains de los subdomains
```
puredns bruteforce [wordlist] [root domain]
```

### DNS Permutations
**admin**-sub.domain.tls
```
cat output | alterx | puredns resolve | anew resolved
```

### Root domain  discovery
- [Crunchbase](https://www.crunchbase.com/)
- Whois / reverse whois
- Whoxy
- Shodan

## Exposición
```sh
cat resolved | httpx -title -status-code -location -ip -cname -tech-detect | anew metadata 
```

- Eyewitness
- HTTP
- gowitness
- httpscreenshot

# Content discovery
Tras resolver hosts vivos, descubrir rutas/endpoints ocultos.

```sh
## Fuzzing de directorios y ficheros
ffuf -u https://target/FUZZ -w wordlist.txt -mc 200,204,301,302,403 -o ffuf.json
feroxbuster -u https://target -w wordlist.txt -x php,bak,old,zip

## Extraer URLs históricas
cat hosts | gau --threads 5 | anew urls          # getallurls (wayback+otb)
cat hosts | waybackurls | anew urls
katana -u https://target -d 3 -jc | anew urls     # crawler activo con JS
```

| Wordlist | Uso |
|----------|-----|
| SecLists `raft-*` | Directorios/ficheros genéricos. |
| `assetnote` | Wordlists de alta señal. |
| `api/*` | Endpoints de API. |
| Custom (de JS) | Generadas con `getJS` + `linkfinder`. |

# Vulns

## Nuclei
Escaneo de vulnerabilidades guiado por plantillas (YAML) de la comunidad.

```sh
cat metadata | nuclei -t cves/ -t exposures/ -severity critical,high -o nuclei.txt
nuclei -update-templates                          # actualizar plantillas
```

## Fuzzing de parámetros
```sh
## Descubrir parámetros ocultos
cat urls | arjun -o params.json
ffuf -u 'https://target/page?FUZZ=test' -w params.txt -fs 0
```

| Clase de bug | Señal / técnica |
|--------------|-----------------|
| IDOR / BOLA | Cambiar IDs (`/user/123` → `/user/124`); comparar respuestas entre cuentas. |
| XSS | Reflejo de input sin sanitizar; probar `"><img src=x onerror=alert(1)>`. |
| SSRF | Parámetros con URLs/host; apuntar a `169.254.169.254` (metadata cloud) y a OAST. |
| SQLi | Comillas → error; probar payloads boolean/time (ver [[SQL_Cheatsheet]]). |
| Open Redirect | Parámetros `?next=`, `?url=`, `?redirect=`. |
| Subdomain takeover | CNAME a servicio no reclamado ([[RECON_CS]], `can-i-take-over-xyz`). |
| CORS mal configurado | `Origin: evil.com` reflejado en `Access-Control-Allow-Origin`. |

## OAST (Out-of-band)
Detectar vulns sin reflejo directo (SSRF ciego, RCE ciego, XXE) vía interacciones DNS/HTTP.
- Burp Collaborator / `interactsh-client`.

# Tooling esencial

| Herramienta | Rol |
|-------------|-----|
| `subfinder` / `amass` | Enumeración de subdominios. |
| `puredns` / `dnsx` | Resolución y bruteforce DNS. |
| `httpx` | Sondeo HTTP, títulos, tech, status. |
| `nuclei` | Escaneo por plantillas. |
| `ffuf` / `feroxbuster` | Fuzzing de contenido. |
| `gau` / `waybackurls` / `katana` | Recolección de URLs. |
| `gf` | Patrones (grep) para clasificar URLs por bug. |
| `anew` | Dedupe incremental en pipelines. |
| `notify` | Notificaciones (Slack/Discord/Telegram). |

# Recursos
### [[RECON_CS]] · [[OSINT_cheatsheet]] · [[Dorks_CS]]
### [ProjectDiscovery Tools](https://github.com/projectdiscovery)
### [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
### [Bug Bounty Methodology — Jhaddix TBHM](https://github.com/jhaddix/tbhm)