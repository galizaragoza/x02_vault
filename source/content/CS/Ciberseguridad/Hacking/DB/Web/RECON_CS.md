# Search engines
- **Baidu**
- **Bing**
- **binsearch.info**
- **Common Crawl**
	- “An open repository of web crawl data that can be accessed and analyzed by anyone.”
- **DuckDuckGo**
	- Search syntax support: [syntax docs](https://duckduckgo.com/duckduckgo-help-pages/results/syntax) 
- **Google** 
	- [Google dork cheatsheet](https://gist.github.com/sundowndev/283efaddbcf896ab405488330d1bbc06)
	- [ExploitDB google hacking](https://www.exploit-db.com/google-hacking-database)
	- Interesante mirar con `site`: pastebin, justpaste, codepen
	- [Google Webmaster Tools](https://search.google.com/search-console/welcome?hl=en)
	- [Google Advanced Search](https://www.google.com/advanced_search)
- **Wayback Machine**
- **Startpage**
- **Shodan**
- **Yandex**
- **Yippy**
- **Biznar**
- **[OSINT Framework](https://osintframework.com/)**
- **[EXA](https://websets.exa.ai/websets)**
- **Search Operators Github**
	- [Search code syntax](https://github.com/github/docs/blob/main/content/search-github/github-code-search/understanding-github-code-search-syntax.md)
	- [Regex Patterns](https://www.sparcflow.com/secret_regex_patterns.txt)
	- [ShhGit](https://github.com/eth0izzle/shhgit/)

# OSINT
- [Taranis AI](https://taranis.ai/)
- Bizum
- [Social Searcher](https://www.social-searcher.com/)
- [Social Catfish](https://socialcatfish.com/)
- [Open buckets](https://buckets.grayhatwarfare.com/)
- [info-perso](https://info-perso.com/es/)
- [OSINT framework](https://osintframework.com/)
- [FOCA](https://github.com/ElevenPaths/FOCA)
- [NAPALM FTP Indexer](https://www.searchftps.net/)
- [Open Source Software Insight](https://ossinsight.io/)
- [Maltego](https://www.maltego.com/)
- [HaveIBeenPwned](https://haveibeenpwned.com/)
# Fingerprint web server
- [Webhook.site](https://webhook.site/#!/view/e5737cb0-c7e4-454f-bec4-2ec878ec7529/f539f18d-e97c-4f86-bd4f-3869052733db/1)
- [Pentest Tools](https://pentest-tools.com/alltools)
- [NetCraft](https://www.netcraft.com/resources/research-tools)
- [IP2Location](https://www.ip2location.com/)
- **Netcraft**
- **Nikto**
- **Nmap**
- **DNS**
	- [Censys](https://search.censys.io/)
	- [[dnsrecon]]
	- [FOFA](https://en.fofa.info/)
	- [[dig]]
	- [DNSDumpster](https://dnsdumpster.com/)
	- [Subdomain finder](https://subdomainfinder.c99.nl/index.php)
- **gobuster**
- **WAF bypass**
	- [ViewDNS](https://viewdns.info/)
	- [Favicon Hash](https://favicon-hash.kmsec.uk/)
	- [MxToolbox](https://mxtoolbox.com/SuperTool.aspx?action=mx%3aayudapyme.es&run=toolpage)
	- Shodan
		- [[Dorks_CS]]
		- [Shodan Dorks](https://github.com/nullfuzz-pentest/shodan-dorks)
- [TTL fingerprint](https://subinsb.com/default-device-ttl-values/) 
- **traceroute**


# Webserver Metafiles
- **robots.txt**
- **\<META> tags**
- **sitemap.xml**
- **security.txt**
- **/.well-known**
	- [Well known URIs](https://en.wikipedia.org/wiki/Well-known_URI#List_of_well-known_URIs)
- **humans.txt**

# Enumerar aplicaciones en un WebServer
- **Directory browsing**
- **Referencias externas (search engines)**
- **Fuzzing**
- **Non-standard ports**
- **Virtual hosts**
- **DNS Zone Transfers**
- **DNS Inverse Queries**
- **Web DNS searches**
	- [Netcraft tool](https://searchdns.netcraft.com/?host)
- **Reverse IP services**
	- Bing: `ip:x.x.x.x`
	- [DNSstuff toolset](https://www.dnsstuff.com/freetools)
- **Search engines**

# Contenido de la web
- **Revisar comentarios y metadata**
- **Identificar y recopilar los archivos JavaScript**
- **Identificar source map files**

# Identificar los puntos de entrada de la app
- **Requests**
	- Identificar los lugares donde se usa `GET` y `POST`
	- Identificar los parámetros enviados `POST`
		- Especial atención a los parámetros `hidden`
	- Identificar los parámetros usados en una petición `GET`
		- Especial atención a los parámetros pasados por URL (normalmente tras un `?`: `ejemplo.com?param=param`)
		- Identificar los parámetros de la query string, normalmente en pares `foo=bar`, recalcar que puede haber varios parámetros concatenados con símbolos como `&`, `\~`, `:`
	- Prestar atención a otros Headers como `debug=false`
- **Responses**
	- Identificar cuándo se setean nuevas Cookies (`Set-Cookie` header), se modifican modifican o añaden.
	- Identificar redirects (300); códigos 400, en particular los 403 y errores de servidor (500)
	- Identificar Headers que puedan resultar en una mayor superficie de ataque, por ejemplo, `Server: BIG-IP` indica que el sitio está [load balanced](https://www.youtube.com/watch?v=LQuuoHTyYz8&pp=ygUObG9hZCBiYWxhbmNpbmc%3D).


# Fingerprint WebApp framework
- **[[whatweb]]**
- **HTTP headers**
	- `X-Powered-By:`
	- `Server:`
	- `X-Generator:`
- **Cookies**
	- [Cookiepedia](https://cookiepedia.co.uk/): "...[all about cookies](https://cookiepedia.co.uk/all-about-cookies), the [types of data](https://cookiepedia.co.uk/types-of-cookies) they collect, and the websites that use them."
- **Código fuente**
	- `<head>`
	- `<meta>`
	- Footer
- **Ciertos archivos y carpetas específicos**
	- `wp-config.php` delata la utilización de WordPress como CMS, por ejemplo.
	- Dirbusting
	- `robots.txt`
- **Extensiones de archivos**
	- `.php` = PHP, `.aspx` = ASP.NET, `.jsp` = Java Server
- **Mensajes de error**
- **General markers**
	- `%framework_name%`
	- `powered by`
	- `built upon`
	- `running`