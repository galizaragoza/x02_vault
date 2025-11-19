# Reconocimiento
### Recopilación de info
- **Browsers**
Es recomendable usar distintos navegadores para recopilar información sobre un objetivo, ya que estos pueden dar distintos resultados y uno podría llegar a exponer información que otros no muestran
	[Baidu](https://www.baidu.com/) #1 en China; [DuckDuckGo (privado)](https://duckduckgo.com/); [Bing](https://www.bing.com/?setlang=es) ; [Common Crawl](https://commoncrawl.org/) "open repository of web crawl data that can be used by anyone."; [Wayback Machine](https://web.archive.org/); [Startpage (privado)](https://www.startpage.com/es/) Resultados de Google sin tracking; [Shodan Engine](https://www.shodan.io/dashboard) Busca servicios y dispositivos expuestos a Internet

- **Google Dorks**
Si el search engine lo soporta, los operadores de búsqueda u opciones avanzadas son potentes herramientas para averiguar más
	[Google Hacking DB](https://www.exploit-db.com/google-hacking-database); `cache:`

- **Banner Grabbing**
Consiste en averiguar la tecnología, versión y otra info sobre el servidor objetivo, mediante técnicas como peticiones malformadas o templating
	 `telnet`; `openssl`; Netcraft; Nikto; Nmap (Zenmap)

- **Metafiles**
Además de perfilar el objetivo averiguando en que servidor corre, es útil investigar posibles archivos que a menudo contienen info jugosa
	Google Webmaster Tools; `robots.txt`; `META` tags; Sitemaps; `security.txt`; `.well-known/`; `humans.txt`
- **Herramientas**
	DevTools, Ver código fuente, Burp Suite, OWASP ZAP



## Fuentes
Web Security Testing Guide 4.2 (OWASP)
