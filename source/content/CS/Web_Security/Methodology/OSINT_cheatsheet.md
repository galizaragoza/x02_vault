# Search Engines
### Baidu
- Número 1 en China 
### Bing
- Permite búsqueda avanzada
### binsearch.info
### Common Crawl
- “An open repository of web crawl data that can be accessed and analyzed by anyone.” ##### DuckDuckGo
- Search syntax support: [syntax docs](https://duckduckgo.com/duckduckgo-help-pages/results/syntax) 
### Google 
- Google operators: [google dork cheatsheet](https://gist.github.com/sundowndev/283efaddbcf896ab405488330d1bbc06)
```
site:
inurl:
intitle:
intext:
inbody:
filetype:
cache:
```
- Google Hacking db: [exploitDB google hacking](https://www.exploit-db.com/google-hacking-database)
	- Los operadores de esta lista pueden revelar información sensible como: footholds, archivos con usernames, directorios sensibles, servidores web, archivos y servidores vulnerables, mensajes de error, archivos con información sensible o contraseñas...
	- Interesante mirar con `site`: pastebin, justpaste, codepen, gist
### Wayback Machine
### Startpage
### Shodan
### Yandex
### Yippy
- "Yippy search engine clusters 39 search engines in one location."
### Biznar

### [OSINT Fraework](https://osintframework.com/)
### Search Operators Github
```
org:OrganizationName password
org:OrganizationName aws_secret_access_key
...
```
[Regex Patterns](https://www.sparcflow.com/secret_regex_patterns.txt), [ShhGit](https://github.com/eth0izzle/shhgit/)

# Extensiones interesantes

## Configuración y logs
- `conf`, `config`, `cfg`
- `log`, `logs`
- `env` ⚠️ muy sensible
- `ini`, `properties`
- `yaml`, `yml`
- `xml`

## Bases de datos y respaldos
- `sql`, `sqlite`, `db`, `db3`
- `bak`, `backup`, `old`, `orig`
- `dump`
- `sql.gz`, `tar.gz`, `zip`

## Credenciales y autenticación
- `pem`, `key`, `crt`, `cer`
- `kdbx`
- `id_rsa`, `id_dsa`
- `htpasswd`, `htaccess`
- `ps1`, `bat`, `sh`

## Documentos con metadatos
- `pdf`
- `doc`, `docx`, `xls`, `xlsx`, `ppt`, `pptx`
- `odt`, `ods`
- `rtf`

## Archivos web y aplicaciones
- `json`
- `jsp`, `asp`, `aspx`, `php`
- `js`
- `wasm`
- `swf`

## Texto y datos estructurados
- `csv`, `tsv`
- `txt`, `text`
- `md`, `markdown`
- `rst`

## Mapas y geolocalización
- `kml`, `kmz`
- `gpx`
- `geojson`

## Imágenes (metadatos EXIF)
- `jpg`, `jpeg`, `png`, `tiff`, `bmp`

## Entornos empresariales
- `vsdx`, `vsd`
- `mdb`, `accdb`
- `pub`
- `msg`, `eml`
- `ics`

## Otras útiles
- `torrent`
- `rss`, `atom`
- `wsdl`
- `pac`

---

## Ejemplos de dorks
# Domains
### censys.io
[Censys]](https://search.censys.io/)
### Sublist3r
### PassiveDNS
### VirusTotal
### Amass
### Fernmelder

# WebInf
### whois

## AWS
### [Open s3 buckets](https://buckets.grayhatwarfare.com/)

---

# Personas (People OSINT)

| Categoría | Recurso |
|-----------|---------|
| Agregadores | [Pipl](https://pipl.com/), [That'sThem](https://thatsthem.com/), [WhitePages](https://www.whitepages.com/) |
| España | [Páginas Blancas](https://blancas.paginasamarillas.es/), padrón/BOE, [InfoEmpresa](https://www.infoempresa.com/) |
| Genealogía / registros | [FamilySearch](https://www.familysearch.org/) |
| Profesional | LinkedIn (+ [Apollo](https://www.apollo.io/), [RocketReach](https://rocketreach.co/)) |

# Email y filtraciones (breaches)

| Recurso | Uso |
|---------|-----|
| [HaveIBeenPwned](https://haveibeenpwned.com/) | ¿Aparece el email en brechas? |
| [Dehashed](https://dehashed.com/) | Búsqueda en bases filtradas (de pago). |
| [IntelligenceX](https://intelx.io/) | Leaks, pastes, darknet. |
| [Hunter.io](https://hunter.io/) | Formato de emails corporativos + verificación. |
| `holehe` | Saber en qué sitios está registrado un email. |
| `h8mail` | Búsqueda de credenciales filtradas. |

# Usernames

| Recurso | Uso |
|---------|-----|
| `sherlock` | Busca un username en cientos de plataformas. |
| `maigret` | Similar, más fuentes + reporte. |
| [WhatsMyName](https://whatsmyname.app/) | Enumeración web de usernames. |
| [Namechk](https://namechk.com/) | Disponibilidad de usernames/dominios. |

# Imágenes y geolocalización

| Recurso | Uso |
|---------|-----|
| [Google Lens](https://lens.google/) / [Yandex Images](https://yandex.com/images/) | Búsqueda inversa (Yandex es fuerte en caras). |
| [TinEye](https://tineye.com/) | Búsqueda inversa + primera aparición. |
| `exiftool` | Metadatos EXIF (GPS, dispositivo, fecha). |
| [GeoGuessr / SunCalc](https://www.suncalc.org/) | Geolocalización por sombras/sol. |
| [Mapillary](https://www.mapillary.com/) / Street View | Verificación de calle. |

# Redes sociales

| Plataforma | Herramienta / técnica |
|------------|-----------------------|
| Twitter/X | [TweetDeck](https://pro.twitter.com/), búsqueda avanzada `from: since: until:` |
| Instagram | [Osintgram](https://github.com/Datalux/Osintgram), `imginn` |
| Telegram | [tgstat](https://tgstat.com/), búsqueda de canales |
| General | [Social Searcher](https://www.social-searcher.com/), [Social Catfish](https://socialcatfish.com/) |

# Teléfonos

| Recurso | Uso |
|---------|-----|
| `phoneinfoga` | Información y huella de un número. |
| [Truecaller](https://www.truecaller.com/) | Identificación de llamadas. |
| Formato internacional | Validar prefijo país/operador (libphonenumber). |

# Recursos
### [[RECON_CS]] · [[Dorks_CS]]
### [OSINT Framework](https://osintframework.com/)
### [Bellingcat's Online Investigation Toolkit](https://bellingcat.gitbook.io/toolkit)
### [IntelTechniques Tools (Michael Bazzell)](https://inteltechniques.com/tools/)
### [Awesome OSINT](https://github.com/jivoi/awesome-osint)