# dnsrecon
Comprobar si las IPs encontradas por `dnsrecon` apuntan a CDNs/WAFs o no, se puede usar `wafw00f` para contrastar.

```sh
dnsrecon -d [domain.tld]
```

```sh
wafw00f [URL]
```


# Shodan SSL
```sh
Ssl.cert.subject.CN:"[domain.tld]" 200

ssl "[domain.tls]" 200
```

Para verificar que el certificado apunta el dominio original:

```sh
nmap --script ssl-cert -p 443 [IP_DE_SHODAN]
```

# Favicon
La idea es obtener la URL del favicon y obtener el hash usando esas webs.
- [Favicon Finder](https://favicons.teamtailor-cdn.com/)
- [Favicon Hash](https://favicon-hash.kmsec.uk/)
Luego, se pueden usar dorks como:
```Shodan
http.favicon.hash:[hash]
```

```Censys
services.http.response.favicons.md5_hash:[hash]
```

# Historial de IPs
Ver el histórico de IPs e ir probando para cruzar resultados o consultar el SPF Record.
- [ViewDNS](https://viewdns.info/)
- [MXToolbox](https://mxtoolbox.com/)
- [SecurityTrails](https://securitytrails.com/)
- Censys
- [FOFA](https://en.fofa.info)
```FOFA
"[domain]" && icon_hash="[hash]" 
```
- [ZoomEye](https://www.zoomeye.ai/)
Permite filtrar por icono e IPv4
- VirusTotal