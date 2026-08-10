`curl` es una herramienta de línea de comandos para transferir datos hacia o desde un servidor mediante multitud de protocolos (HTTP, HTTPS, FTP, FTPS, SFTP, SCP, SMTP, IMAP, POP3, LDAP, TFTP, TELNET, DICT, FILE, etc.). Opera sin interacción del usuario y expone control total sobre métodos HTTP, cabeceras, cuerpo de la petición, autenticación, TLS, proxies y resolución de nombres, lo que la hace central en pruebas de API, automatización y pentesting web. Al contrario que [[wget]] (orientado a descarga recursiva), `curl` está diseñada para transferencia de un único recurso con máxima configurabilidad de la petición. Toda la lógica de red vive en `libcurl`, por lo que `--libcurl` puede volcar el equivalente en C de cualquier invocación.

```bash
curl [opciones] <URL>
```

> Guía basada en `curl 8.21.0`. La disponibilidad de algunas opciones depende del backend TLS (OpenSSL, GnuTLS, Schannel, wolfSSL…) y de cómo se compiló `libcurl`. Verificar con `curl -V`.

---

## Índice
- [[#Sintaxis, URLs y globbing]]
- [[#Métodos HTTP y envío de datos (POST)]]
- [[#Cabeceras HTTP y comportamiento de la petición]]
- [[#Versiones del protocolo HTTP]]
- [[#Redirecciones]]
- [[#Cookies, caché y condiciones]]
- [[#Autenticación]]
- [[#TLS/SSL — certificados, cifrado y versiones]]
- [[#DNS y resolución de nombres]]
- [[#Proxies]]
- [[#Conexión, timeouts, reintentos y rendimiento]]
- [[#Transferencias en paralelo]]
- [[#Formateo de output y ficheros]]
- [[#Trazado, verbose y depuración]]
- [[#La variable `--write-out`]]
- [[#Subida de ficheros (upload)]]
- [[#FTP / FTPS]]
- [[#SFTP / SCP / SSH]]
- [[#Correo — SMTP / IMAP / POP3]]
- [[#TFTP / TELNET / LDAP / FILE / IPFS]]
- [[#Opciones globales y de la herramienta]]
- [[#Opciones heredadas (deprecated)]]
- [[#Protips]]

---

## Sintaxis, URLs y globbing

Definición del/los recurso(s) objetivo y expansión de patrones en la URL.

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--url <url>` | Especifica la URL de trabajo (útil en ficheros de config). | `curl --url https://api.com/v1` |
| `-g`, `--globoff` | Desactiva el globbing de `{}` y `[]`, tratándolos como literales. | `curl -g "https://site.com/[raw]"` |
| `{a,b,c}` | Globbing: genera una petición por cada elemento de la lista. | `curl "https://site.com/{a,b,c}.html"` |
| `[1-10]` | Globbing: itera un rango numérico o alfabético. | `curl "https://site.com/page[1-10].html"` |
| `[00-99]` | Globbing con padding de ceros (rango con ancho fijo). | `curl "https://site.com/id[001-100]"` |
| `#1`, `#2` | Referencia al valor de un glob en el output (`-o`). | `curl "https://site.com/{a,b}" -o "#1.html"` |
| `-:`, `--next` | Separa opciones/URLs: la siguiente URL usa su propio juego de flags. | `curl -d x url1 --next -d y url2` |
| `--proto <protocols>` | Habilita/deshabilita protocolos permitidos (`+`, `-`, `=`). | `curl --proto -all,https https://site.com` |
| `--proto-default <proto>` | Protocolo asumido si la URL no lleva esquema. | `curl --proto-default https site.com` |
| `--proto-redir <protocols>` | Protocolos permitidos al seguir redirecciones. | `curl --proto-redir =https -L http://site.com` |

---

## Métodos HTTP y envío de datos (POST)

Definición del verbo HTTP y del cuerpo (payload) de la petición.

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-X`, `--request <método>` | Método HTTP a usar (GET, POST, PUT, DELETE, PATCH…). | `curl -X DELETE https://api.com/user/1` |
| `--request-target <path>` | Fija el request-target en la línea de petición (bypass de rutas). | `curl --request-target "*" -X OPTIONS site.com` |
| `-d`, `--data <data>` | Datos POST `application/x-www-form-urlencoded`; `@fichero` lee de disco. | `curl -d "user=admin&pass=123" https://site.com/login` |
| `--data-ascii <data>` | Alias de `-d` (POST en texto ASCII). | `curl --data-ascii "a=1" https://site.com` |
| `--data-raw <data>` | Como `-d` pero NO interpreta el prefijo `@`. | `curl --data-raw '{"id":1}' https://api.com` |
| `--data-binary <data>` | Envía los datos exactamente como están (sin quitar saltos de línea). | `curl --data-binary @file.json https://api.com` |
| `--data-urlencode <data>` | URL-encodea el dato antes de enviarlo. | `curl --data-urlencode "q=a b&c" https://site.com` |
| `--json <data>` | Atajo: fija `Content-Type` y `Accept` a `application/json` y hace POST. | `curl --json '{"k":"v"}' https://api.com` |
| `-F`, `--form <name=content>` | Datos `multipart/form-data`; `@` adjunta fichero, `<` lee contenido. | `curl -F "file=@img.png" https://upload.com` |
| `--form-string <name=string>` | Como `-F` pero trata `@`/`<` como literales (sin adjuntar). | `curl --form-string "note=@home" https://site.com` |
| `--form-escape` | Escapa los nombres de campo del formulario con backslash. | `curl --form-escape -F "a\"b=1" https://site.com` |
| `-G`, `--get` | Convierte los datos `-d`/`--data-urlencode` en query string y usa GET. | `curl -G -d "q=curl" https://google.com/search` |
| `--url-query <data>` | Añade contenido a la query string de la URL (con URL-encode). | `curl --url-query "page=2" https://api.com/list` |
| `-I`, `--head` | Petición HEAD: solo cabeceras del recurso. | `curl -I https://site.com` |

---

## Cabeceras HTTP y comportamiento de la petición

Metadatos de la petición y control del cuerpo de la respuesta.

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-H`, `--header <header/@file>` | Añade/modifica una cabecera; `@fichero` carga varias; `Cabecera;` la envía vacía. | `curl -H "X-Api-Key: abc" https://api.com` |
| `-A`, `--user-agent <name>` | Fija la cabecera `User-Agent`. | `curl -A "Mozilla/5.0" https://site.com` |
| `-e`, `--referer <URL>` | Fija `Referer`; `;auto` con `-L` lo autogenera. | `curl -e "https://google.com" https://site.com` |
| `--compressed` | Solicita respuesta comprimida (gzip/deflate/br/zstd) y la descomprime. | `curl --compressed https://site.com` |
| `--tr-encoding` | Solicita `Transfer-Encoding` comprimido y lo descomprime. | `curl --tr-encoding https://site.com` |
| `--raw` | Desactiva toda descodificación de transferencia/contenido. | `curl --raw https://site.com` |
| `-r`, `--range <range>` | Solicita solo un rango de bytes (Range requests). | `curl -r 0-1023 https://site.com/big.iso` |
| `--ignore-content-length` | Ignora la cabecera `Content-Length` del servidor. | `curl --ignore-content-length https://site.com` |
| `--path-as-is` | No colapsa secuencias `../` y `./` en la ruta de la URL. | `curl --path-as-is https://site.com/../etc/passwd` |
| `--expect100-timeout <s>` | Tiempo de espera de un `100-continue` antes de mandar el cuerpo. | `curl --expect100-timeout 2 -d @big https://api.com` |
| `--alt-svc <file>` | Habilita el caché `Alt-Svc` en el fichero indicado. | `curl --alt-svc altsvc.txt https://site.com` |
| `--hsts <file>` | Habilita HSTS usando ese fichero de caché. | `curl --hsts hsts.txt https://site.com` |

---

## Versiones del protocolo HTTP

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-0`, `--http1.0` | Fuerza HTTP/1.0. | `curl --http1.0 https://site.com` |
| `--http1.1` | Fuerza HTTP/1.1. | `curl --http1.1 https://site.com` |
| `--http2` | Usa HTTP/2 si el servidor lo negocia (vía ALPN/Upgrade). | `curl --http2 https://site.com` |
| `--http2-prior-knowledge` | HTTP/2 en claro sin upgrade desde HTTP/1.1. | `curl --http2-prior-knowledge http://site.com` |
| `--http3` | Intenta HTTP/3 (QUIC), con fallback. | `curl --http3 https://site.com` |
| `--http3-only` | HTTP/3 obligatorio, sin fallback. | `curl --http3-only https://site.com` |
| `--http0.9` | Permite respuestas HTTP/0.9 (sin cabeceras). | `curl --http0.9 http://legacy.com` |

---

## Redirecciones

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-L`, `--location` | Sigue redirecciones (cabecera `Location`). | `curl -L http://site.com` |
| `--location-trusted` | Como `-L` pero reenvía credenciales/cookies a otros hosts (peligroso). | `curl --location-trusted -u a:b http://site.com` |
| `--follow` | Sigue redirecciones según la especificación (variante estricta). | `curl --follow http://site.com` |
| `--max-redirs <num>` | Límite de saltos de redirección (`-1` = sin límite). | `curl -L --max-redirs 5 http://site.com` |
| `--post301` | No convertir POST→GET tras un 301. | `curl -L --post301 -d x http://site.com` |
| `--post302` | No convertir POST→GET tras un 302. | `curl -L --post302 -d x http://site.com` |
| `--post303` | No convertir POST→GET tras un 303. | `curl -L --post303 -d x http://site.com` |

---

## Cookies, caché y condiciones

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-b`, `--cookie <data\|file>` | Envía cookies desde string `k=v` o carga un cookie file (Netscape). | `curl -b "session=abc123" https://site.com` |
| `-c`, `--cookie-jar <file>` | Guarda las cookies recibidas en un fichero al terminar. | `curl -c cookies.txt https://site.com` |
| `-j`, `--junk-session-cookies` | Descarta las cookies de sesión leídas del fichero. | `curl -j -b cookies.txt https://site.com` |
| `-z`, `--time-cond <time>` | Transfiere solo si el recurso es más nuevo/viejo que la fecha o fichero. | `curl -z "2026-01-01" https://site.com/f` |
| `--etag-compare <file>` | Envía `If-None-Match` con el ETag leído del fichero. | `curl --etag-compare etag.txt https://site.com` |
| `--etag-save <file>` | Guarda el ETag de la respuesta en un fichero. | `curl --etag-save etag.txt https://site.com` |

---

## Autenticación

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-u`, `--user <user:pass>` | Credenciales para autenticación al servidor. | `curl -u admin:pass https://api.com` |
| `--basic` | Fuerza autenticación HTTP Basic. | `curl --basic -u a:b https://api.com` |
| `--digest` | Autenticación HTTP Digest. | `curl --digest -u a:b https://api.com` |
| `--ntlm` | Autenticación HTTP NTLM. | `curl --ntlm -u dom\\user:pass https://site.com` |
| `--ntlm-wb` | NTLM delegando en el helper `ntlmauth`/winbind. | `curl --ntlm-wb -u user https://site.com` |
| `--negotiate` | Autenticación Negotiate (SPNEGO/Kerberos). | `curl --negotiate -u : https://site.com` |
| `--anyauth` | Deja que `curl` elija el método más seguro ofrecido. | `curl --anyauth -u a:b https://api.com` |
| `--aws-sigv4 <prov>` | Firma la petición con AWS Signature V4. | `curl --aws-sigv4 "aws:amz:eu-west-1:s3" -u KEY:SECRET ...` |
| `--oauth2-bearer <token>` | Envía un token Bearer OAuth2 (`Authorization: Bearer`). | `curl --oauth2-bearer eyJ... https://api.com` |
| `-n`, `--netrc` | Lee credenciales de `~/.netrc`. | `curl -n https://api.com` |
| `--netrc-file <file>` | Especifica un fichero netrc alternativo. | `curl --netrc-file creds https://api.com` |
| `--netrc-optional` | Usa netrc si existe, si no las de la URL. | `curl --netrc-optional https://api.com` |
| `--disallow-username-in-url` | Rechaza URLs que incluyan usuario. | `curl --disallow-username-in-url https://u@site.com` |
| `--krb <level>` | Habilita Kerberos con nivel de seguridad (FTP). | `curl --krb private ftp://site.com` |
| `--delegation <level>` | Permiso de delegación de credenciales GSS-API. | `curl --delegation always --negotiate https://site.com` |
| `--service-name <name>` | Nombre de servicio SPNEGO. | `curl --service-name HTTP --negotiate https://site.com` |
| `--sasl-authzid <id>` | Identidad de autorización para SASL PLAIN. | `curl --sasl-authzid user imap://mail.com` |
| `--sasl-ir` | Envía respuesta inicial en la autenticación SASL. | `curl --sasl-ir imap://mail.com` |
| `--login-options <opts>` | Opciones de login del servidor (IMAP/POP3/SMTP). | `curl --login-options 'AUTH=NTLM' imap://mail.com` |

---

## TLS/SSL — certificados, cifrado y versiones

Ver también [[OpenSSL]] para generación e inspección de certificados.

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-k`, `--insecure` | No verifica el certificado del servidor. | `curl -k https://self-signed.com` |
| `--cacert <file>` | Fichero CA con el que verificar al peer. | `curl --cacert ca.pem https://site.com` |
| `--capath <dir>` | Directorio de CAs para verificar al peer. | `curl --capath /etc/ssl/certs https://site.com` |
| `--ca-native` | Usa el almacén de CAs nativo del SO. | `curl --ca-native https://site.com` |
| `-E`, `--cert <cert[:pass]>` | Certificado de cliente (mTLS). | `curl -E client.pem:pass https://site.com` |
| `--cert-type <type>` | Tipo de certificado (PEM/DER/ENG/PROV/P12). | `curl --cert-type P12 -E cli.p12 https://site.com` |
| `--cert-status` | Verifica el estado OCSP-staple del cert del servidor. | `curl --cert-status https://site.com` |
| `--key <file>` | Fichero de clave privada del certificado de cliente. | `curl -E cert.pem --key key.pem https://site.com` |
| `--key-type <type>` | Tipo de la clave privada (PEM/DER/ENG). | `curl --key-type DER --key k.der https://site.com` |
| `--pass <phrase>` | Passphrase de la clave privada. | `curl --key k.pem --pass s3cr3t https://site.com` |
| `--pubkey <key>` | Clave pública (SSH). | `curl --pubkey id.pub sftp://site.com` |
| `--ciphers <list>` | Cifrados TLS 1.0–1.2 a usar. | `curl --ciphers ECDHE-RSA-AES128-GCM-SHA256 https://site.com` |
| `--tls13-ciphers <list>` | Suites de cifrado TLS 1.3. | `curl --tls13-ciphers TLS_AES_256_GCM_SHA384 https://site.com` |
| `--curves <list>` | Grupos/curvas de intercambio de claves (EC). | `curl --curves X25519 https://site.com` |
| `--sigalgs <list>` | Algoritmos de firma TLS a ofrecer. | `curl --sigalgs ecdsa_secp256r1_sha256 https://site.com` |
| `--crlfile <file>` | Lista de revocación de certificados (CRL). | `curl --crlfile crl.pem https://site.com` |
| `--pinnedpubkey <hashes>` | Fija (pinning) la clave pública esperada del peer. | `curl --pinnedpubkey sha256//base64== https://site.com` |
| `--cert-status` | (ver arriba) OCSP stapling. | — |
| `--ssl` | Intenta usar TLS (protocolos con STARTTLS opcional). | `curl --ssl ftp://site.com` |
| `--ssl-reqd` | Exige TLS; falla si no se puede negociar. | `curl --ssl-reqd ftp://site.com` |
| `--ssl-allow-beast` | Permite el fallo BEAST para mejorar interoperabilidad. | `curl --ssl-allow-beast https://old.com` |
| `--ssl-auto-client-cert` | Cert de cliente automático (Schannel). | `curl --ssl-auto-client-cert https://site.com` |
| `--ssl-no-revoke` | Desactiva comprobación de revocación (Schannel). | `curl --ssl-no-revoke https://site.com` |
| `--ssl-revoke-best-effort` | Ignora puntos de distribución de CRL ausentes (Schannel). | `curl --ssl-revoke-best-effort https://site.com` |
| `--ssl-sessions <file>` | Carga/guarda tickets de sesión TLS. | `curl --ssl-sessions sess.txt https://site.com` |
| `--no-sessionid` | Desactiva la reutilización de SSL session-ID. | `curl --no-sessionid https://site.com` |
| `--no-alpn` | Desactiva la extensión TLS ALPN. | `curl --no-alpn https://site.com` |
| `--no-npn` | Desactiva la extensión TLS NPN. | `curl --no-npn https://site.com` |
| `--false-start` | Habilita TLS False Start. | `curl --false-start https://site.com` |
| `--tls-earlydata` | Permite early data TLS 1.3 (0-RTT). | `curl --tls-earlydata https://site.com` |
| `--tlsauthtype <type>` | Tipo de autenticación TLS (p. ej. `SRP`). | `curl --tlsauthtype SRP https://site.com` |
| `--tlsuser <name>` | Usuario para autenticación TLS-SRP. | `curl --tlsuser user --tlspassword p https://site.com` |
| `--tlspassword <str>` | Contraseña para autenticación TLS-SRP. | `curl --tlsuser u --tlspassword s3cr3t https://site.com` |
| `-1`, `--tlsv1` | TLS 1.0 o superior. | `curl -1 https://site.com` |
| `--tlsv1.0` | TLS 1.0 o superior. | `curl --tlsv1.0 https://site.com` |
| `--tlsv1.1` | TLS 1.1 o superior. | `curl --tlsv1.1 https://site.com` |
| `--tlsv1.2` | TLS 1.2 o superior. | `curl --tlsv1.2 https://site.com` |
| `--tlsv1.3` | TLS 1.3 o superior. | `curl --tlsv1.3 https://site.com` |
| `--tls-max <ver>` | Versión TLS máxima permitida. | `curl --tls-max 1.2 https://site.com` |
| `--engine <name>` | Motor criptográfico de OpenSSL a usar. | `curl --engine pkcs11 https://site.com` |
| `--ech <config>` | Configura Encrypted Client Hello. | `curl --ech true https://site.com` |
| `--doh-url <URL>` | Resuelve hostnames vía DNS-over-HTTPS. | `curl --doh-url https://dns.google/dns-query https://site.com` |
| `--doh-insecure` | Permite conexión DoH sin verificar cert. | `curl --doh-insecure --doh-url https://... https://site.com` |
| `--doh-cert-status` | Verifica estado OCSP del servidor DoH. | `curl --doh-cert-status --doh-url https://... https://site.com` |

---

## DNS y resolución de nombres

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-4`, `--ipv4` | Resuelve solo a direcciones IPv4. | `curl -4 https://site.com` |
| `-6`, `--ipv6` | Resuelve solo a direcciones IPv6. | `curl -6 https://site.com` |
| `--resolve <host:port:addr>` | Fuerza la IP para un host:puerto (bypass de DNS). | `curl --resolve site.com:443:127.0.0.1 https://site.com` |
| `--connect-to <H1:P1:H2:P2>` | Conecta a host2:puerto2 manteniendo el host original en la petición. | `curl --connect-to site.com:443:1.2.3.4:443 https://site.com` |
| `--dns-servers <addrs>` | Servidores DNS alternativos (requiere c-ares). | `curl --dns-servers 8.8.8.8 https://site.com` |
| `--dns-interface <iface>` | Interfaz de salida para las consultas DNS. | `curl --dns-interface eth0 https://site.com` |
| `--dns-ipv4-addr <addr>` | IPv4 local de origen para las consultas DNS. | `curl --dns-ipv4-addr 10.0.0.2 https://site.com` |
| `--dns-ipv6-addr <addr>` | IPv6 local de origen para las consultas DNS. | `curl --dns-ipv6-addr fe80::1 https://site.com` |
| `--happy-eyeballs-timeout-ms <ms>` | Margen para IPv6 antes de probar IPv4. | `curl --happy-eyeballs-timeout-ms 200 https://site.com` |

---

## Proxies

Encadenable con [[proxychains]]; ver también HTTP CONNECT tunneling.

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-x`, `--proxy <[proto://]host[:port]>` | Usa un proxy para la conexión. | `curl -x http://127.0.0.1:8080 https://site.com` |
| `--preproxy <host[:port]>` | Proxy SOCKS previo al proxy HTTP(S). | `curl --preproxy socks5://127.0.0.1:9050 -x http://p:8080 site.com` |
| `-U`, `--proxy-user <user:pass>` | Credenciales del proxy. | `curl -U admin:pass -x http://p:8080 site.com` |
| `-p`, `--proxytunnel` | Tunela protocolos no-HTTP vía CONNECT. | `curl -p -x http://p:8080 ftp://site.com` |
| `--proxy-anyauth` | Elige cualquier método de auth del proxy. | `curl --proxy-anyauth -U a:b -x p:8080 site.com` |
| `--proxy-basic` | Basic auth contra el proxy. | `curl --proxy-basic -U a:b -x p:8080 site.com` |
| `--proxy-digest` | Digest auth contra el proxy. | `curl --proxy-digest -U a:b -x p:8080 site.com` |
| `--proxy-negotiate` | Negotiate (SPNEGO) contra el proxy. | `curl --proxy-negotiate -U : -x p:8080 site.com` |
| `--proxy-ntlm` | NTLM contra el proxy. | `curl --proxy-ntlm -U a:b -x p:8080 site.com` |
| `--proxy-service-name <name>` | Nombre de servicio SPNEGO del proxy. | `curl --proxy-service-name HTTP -x p:8080 site.com` |
| `--proxy-header <header/@file>` | Cabeceras propias hacia el proxy. | `curl --proxy-header "X-P: 1" -x p:8080 site.com` |
| `--proxy-insecure` | No verifica el cert del proxy HTTPS. | `curl --proxy-insecure -x https://p:8443 site.com` |
| `--proxy-cacert <file>` | CA para verificar el proxy HTTPS. | `curl --proxy-cacert ca.pem -x https://p:8443 site.com` |
| `--proxy-capath <dir>` | Directorio de CAs para el proxy. | `curl --proxy-capath /certs -x https://p:8443 site.com` |
| `--proxy-ca-native` | CAs del SO para verificar el proxy. | `curl --proxy-ca-native -x https://p:8443 site.com` |
| `--proxy-cert <cert[:pass]>` | Cert de cliente para el proxy HTTPS. | `curl --proxy-cert c.pem -x https://p:8443 site.com` |
| `--proxy-cert-type <type>` | Tipo de cert de cliente del proxy. | `curl --proxy-cert-type PEM -x https://p:8443 site.com` |
| `--proxy-key <key>` | Clave privada para el proxy HTTPS. | `curl --proxy-key k.pem -x https://p:8443 site.com` |
| `--proxy-key-type <type>` | Tipo de la clave del proxy. | `curl --proxy-key-type PEM -x https://p:8443 site.com` |
| `--proxy-pass <phrase>` | Passphrase de la clave del proxy. | `curl --proxy-pass s3cr3t -x https://p:8443 site.com` |
| `--proxy-ciphers <list>` | Cifrados TLS 1.0–1.2 para el proxy. | `curl --proxy-ciphers DEFAULT -x https://p:8443 site.com` |
| `--proxy-tls13-ciphers <list>` | Suites TLS 1.3 para el proxy. | `curl --proxy-tls13-ciphers TLS_AES_128_GCM_SHA256 -x https://p site.com` |
| `--proxy-crlfile <file>` | CRL para el proxy. | `curl --proxy-crlfile crl.pem -x https://p:8443 site.com` |
| `--proxy-pinnedpubkey <hashes>` | Pinning de clave pública del proxy. | `curl --proxy-pinnedpubkey sha256//... -x https://p site.com` |
| `--proxy-tlsauthtype <type>` | Tipo de auth TLS del proxy. | `curl --proxy-tlsauthtype SRP -x https://p site.com` |
| `--proxy-tlsuser <name>` | Usuario TLS-SRP del proxy. | `curl --proxy-tlsuser u -x https://p site.com` |
| `--proxy-tlspassword <str>` | Contraseña TLS-SRP del proxy. | `curl --proxy-tlspassword s -x https://p site.com` |
| `--proxy-tlsv1` | Fuerza TLS 1.x contra el proxy HTTPS. | `curl --proxy-tlsv1 -x https://p:8443 site.com` |
| `--proxy-ssl-allow-beast` | Permite el fallo BEAST en el proxy HTTPS. | `curl --proxy-ssl-allow-beast -x https://p site.com` |
| `--proxy-ssl-auto-client-cert` | Cert de cliente automático para el proxy. | `curl --proxy-ssl-auto-client-cert -x https://p site.com` |
| `--proxy-http2` | Usa HTTP/2 contra el proxy HTTPS. | `curl --proxy-http2 -x https://p:8443 site.com` |
| `--proxy-http3` | Usa HTTP/3 contra el proxy HTTPS. | `curl --proxy-http3 -x https://p:8443 site.com` |
| `--proxy1.0 <host[:port]>` | Proxy HTTP/1.0 en el puerto dado. | `curl --proxy1.0 p:8080 site.com` |
| `--noproxy <lista>` | Hosts que NO deben pasar por el proxy. | `curl --noproxy "localhost,.lan" -x p:8080 site.com` |
| `--socks4 <host[:port]>` | Proxy SOCKS4. | `curl --socks4 127.0.0.1:1080 site.com` |
| `--socks4a <host[:port]>` | Proxy SOCKS4a (resuelve nombre en el proxy). | `curl --socks4a 127.0.0.1:1080 site.com` |
| `--socks5 <host[:port]>` | Proxy SOCKS5 (resolución local). | `curl --socks5 127.0.0.1:9050 site.com` |
| `--socks5-hostname <host[:port]>` | SOCKS5 resolviendo el nombre en el proxy (uso Tor). | `curl --socks5-hostname 127.0.0.1:9050 http://x.onion` |
| `--socks5-basic` | Auth usuario/contraseña para SOCKS5. | `curl --socks5-basic --socks5 p:1080 -U a:b site.com` |
| `--socks5-gssapi` | Habilita GSS-API para SOCKS5. | `curl --socks5-gssapi --socks5 p:1080 site.com` |
| `--socks5-gssapi-nec` | Compatibilidad con servidor SOCKS5 de NEC. | `curl --socks5-gssapi-nec --socks5 p:1080 site.com` |
| `--socks5-gssapi-service <name>` | Nombre de servicio GSS-API del proxy SOCKS5. | `curl --socks5-gssapi-service rcmd --socks5 p site.com` |
| `--haproxy-protocol` | Envía cabecera PROXY protocol v1 de HAProxy. | `curl --haproxy-protocol http://backend:80` |
| `--haproxy-clientip <ip>` | IP de cliente a poner en la cabecera PROXY. | `curl --haproxy-clientip 1.2.3.4 http://backend:80` |
| `--suppress-connect-headers` | Oculta las cabeceras de respuesta del CONNECT. | `curl -p --suppress-connect-headers -x p:8080 site.com` |

---

## Conexión, timeouts, reintentos y rendimiento

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--connect-timeout <s>` | Tiempo máximo para establecer la conexión. | `curl --connect-timeout 5 https://site.com` |
| `-m`, `--max-time <s>` | Tiempo máximo para toda la operación (acepta decimales). | `curl -m 10 https://slow.com` |
| `--retry <num>` | Número de reintentos ante errores transitorios. | `curl --retry 3 https://unstable.com` |
| `--retry-all-errors` | Reintenta ante cualquier error (con `--retry`). | `curl --retry 3 --retry-all-errors https://site.com` |
| `--retry-connrefused` | Reintenta también si la conexión es rechazada. | `curl --retry 3 --retry-connrefused https://site.com` |
| `--retry-delay <s>` | Espera fija entre reintentos. | `curl --retry 3 --retry-delay 5 https://site.com` |
| `--retry-max-time <s>` | Ventana total máxima para reintentos. | `curl --retry 99 --retry-max-time 60 https://site.com` |
| `--limit-rate <speed>` | Limita la velocidad de transferencia (`k`, `M`, `G`). | `curl --limit-rate 200k https://site.com/f` |
| `--max-filesize <bytes>` | Aborta si el recurso supera este tamaño. | `curl --max-filesize 1M https://site.com/f` |
| `-Y`, `--speed-limit <speed>` | Velocidad mínima (bytes/s) antes de abortar. | `curl -Y 1000 -y 10 https://site.com/f` |
| `-y`, `--speed-time <s>` | Ventana para evaluar `--speed-limit`. | `curl -Y 1000 -y 30 https://site.com/f` |
| `-C`, `--continue-at <offset>` | Reanuda una transferencia desde un offset (`-` = auto). | `curl -C - -O https://site.com/big.iso` |
| `--keepalive-time <s>` | Intervalo entre sondas TCP keepalive. | `curl --keepalive-time 60 https://site.com` |
| `--keepalive-cnt <n>` | Número máximo de sondas keepalive. | `curl --keepalive-cnt 3 https://site.com` |
| `--no-keepalive` | Desactiva TCP keepalive. | `curl --no-keepalive https://site.com` |
| `--tcp-nodelay` | Activa `TCP_NODELAY` (desactiva Nagle). | `curl --tcp-nodelay https://site.com` |
| `--tcp-fastopen` | Usa TCP Fast Open. | `curl --tcp-fastopen https://site.com` |
| `--mptcp` | Habilita Multipath TCP. | `curl --mptcp https://site.com` |
| `--local-port <range>` | Rango de puertos locales de origen. | `curl --local-port 50000-50100 https://site.com` |
| `--interface <name>` | Interfaz/IP/host de red de salida. | `curl --interface eth0 https://site.com` |
| `--unix-socket <path>` | Conecta a través de un socket Unix. | `curl --unix-socket /var/run/docker.sock http://localhost/info` |
| `--abstract-unix-socket <path>` | Conecta vía socket Unix abstracto. | `curl --abstract-unix-socket @sock http://localhost/` |
| `--ip-tos <string>` | Fija IP Type of Service / Traffic Class. | `curl --ip-tos throughput https://site.com` |
| `--vlan-priority <prio>` | Fija la prioridad VLAN (0–7). | `curl --vlan-priority 5 https://site.com` |

---

## Transferencias en paralelo

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-Z`, `--parallel` | Realiza las transferencias (múltiples URLs) en paralelo. | `curl -Z -O url1 -O url2 -O url3` |
| `--parallel-max <num>` | Concurrencia máxima en modo paralelo. | `curl -Z --parallel-max 4 -O url[1-20]` |
| `--parallel-max-host <num>` | Máximo de conexiones a un mismo host. | `curl -Z --parallel-max-host 2 -O url[1-20]` |
| `--parallel-immediate` | No espera al multiplexado; abre conexiones ya. | `curl -Z --parallel-immediate -O url[1-9]` |
| `--rate <max rate>` | Frecuencia máxima de peticiones en modo serie. | `curl --rate 10/s -O url[1-100]` |

---

## Formateo de output y ficheros

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-o`, `--output <file>` | Escribe la respuesta en un fichero (`#N` para globs). | `curl -o out.html https://site.com` |
| `-O`, `--remote-name` | Guarda con el nombre de fichero remoto. | `curl -O https://site.com/file.zip` |
| `--remote-name-all` | Aplica `-O` a todas las URLs. | `curl --remote-name-all url1 url2` |
| `-J`, `--remote-header-name` | Usa el nombre de `Content-Disposition` (con `-O`). | `curl -OJ https://site.com/download` |
| `--output-dir <dir>` | Directorio donde guardar los ficheros. | `curl --output-dir ./dl -O https://site.com/f` |
| `--create-dirs` | Crea la jerarquía de directorios local necesaria. | `curl --create-dirs -o a/b/c.html https://site.com` |
| `--create-file-mode <mode>` | Permisos (octal) de los ficheros creados. | `curl --create-file-mode 0600 -o f https://site.com` |
| `-R`, `--remote-time` | Conserva la fecha del recurso remoto en el fichero local. | `curl -R -O https://site.com/f` |
| `--remove-on-error` | Borra el fichero de salida si hay error. | `curl --remove-on-error -o f https://site.com` |
| `--no-clobber` | No sobrescribe ficheros existentes (renombra). | `curl --no-clobber -O https://site.com/f` |
| `--skip-existing` | Omite la descarga si el fichero local ya existe. | `curl --skip-existing -O https://site.com/f` |
| `--out-null` | Descarta el cuerpo de la respuesta. | `curl --out-null -w "%{http_code}" https://site.com` |
| `-D`, `--dump-header <file>` | Guarda las cabeceras de respuesta en un fichero. | `curl -D headers.txt https://site.com` |
| `-i`, `--show-headers` | Incluye las cabeceras de respuesta en la salida (antes `--include`). | `curl -i https://site.com` |
| `-s`, `--silent` | Modo silencioso: sin progreso ni errores. | `curl -s https://site.com` |
| `-S`, `--show-error` | Muestra errores aun con `-s`. | `curl -sS https://site.com` |
| `-f`, `--fail` | Falla sin output ante errores HTTP (≥400). | `curl -f https://site.com/missing` |
| `--fail-early` | Aborta en el primer fallo (varias URLs). | `curl --fail-early url1 url2` |
| `--fail-with-body` | Falla ante error HTTP pero conserva el cuerpo. | `curl --fail-with-body https://site.com` |
| `-#`, `--progress-bar` | Muestra el progreso como barra. | `curl -# -O https://site.com/big.iso` |
| `--no-progress-meter` | Oculta el medidor de progreso (mantiene errores). | `curl --no-progress-meter https://site.com` |
| `-N`, `--no-buffer` | Desactiva el buffer de la salida (streaming). | `curl -N https://site.com/stream` |
| `--styled-output` | Salida con estilo (negrita) para cabeceras HTTP. | `curl --styled-output -I https://site.com` |
| `-w`, `--write-out <format>` | Imprime variables tras la transferencia (ver sección propia). | `curl -w "%{http_code}\n" -o /dev/null -s https://site.com` |
| `--xattr` | Guarda metadatos (URL origen) en atributos extendidos. | `curl --xattr -O https://site.com/f` |

---

## Trazado, verbose y depuración

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-v`, `--verbose` | Muestra detalle de la transacción (`>` envío, `<` recepción). | `curl -v https://site.com` |
| `--trace <file>` | Vuelca un trace completo con hex a un fichero (`-` = stdout). | `curl --trace trace.txt https://site.com` |
| `--trace-ascii <file>` | Como `--trace` pero sin la columna hexadecimal. | `curl --trace-ascii - https://site.com` |
| `--trace-config <string>` | Selecciona qué detalles registrar en el trace/verbose. | `curl --trace-config "http/2,tls" -v https://site.com` |
| `--trace-ids` | Añade IDs de transferencia y conexión al verbose. | `curl --trace-ids -v https://site.com` |
| `--trace-time` | Añade marcas de tiempo al trace/verbose. | `curl --trace-time -v https://site.com` |
| `--stderr <file>` | Redirige stderr a un fichero (`-` = stdout). | `curl --stderr err.log -v https://site.com` |
| `--libcurl <file>` | Genera el código C libcurl equivalente al comando. | `curl --libcurl code.c https://site.com` |

---

## La variable `--write-out`

`-w` acepta un formato con variables `%{...}`. Útil para medir, extraer estado o construir scripts.

| Variable | Descripción | Ejemplo |
|---|---|---|
| `%{http_code}` | Código de estado HTTP de la última respuesta. | `curl -o /dev/null -s -w "%{http_code}\n" https://site.com` |
| `%{time_total}` | Tiempo total de la operación (s). | `curl -o /dev/null -s -w "%{time_total}\n" https://site.com` |
| `%{time_namelookup}` | Tiempo hasta resolver DNS. | `curl -s -o /dev/null -w "dns=%{time_namelookup}\n" https://site.com` |
| `%{time_connect}` | Tiempo hasta completar el TCP. | `curl -s -o /dev/null -w "tcp=%{time_connect}\n" https://site.com` |
| `%{time_appconnect}` | Tiempo hasta completar el handshake TLS. | `curl -s -o /dev/null -w "tls=%{time_appconnect}\n" https://site.com` |
| `%{time_starttransfer}` | Tiempo hasta el primer byte (TTFB). | `curl -s -o /dev/null -w "ttfb=%{time_starttransfer}\n" https://site.com` |
| `%{size_download}` | Bytes descargados. | `curl -s -o /dev/null -w "%{size_download}\n" https://site.com` |
| `%{speed_download}` | Velocidad media de descarga (bytes/s). | `curl -s -o /dev/null -w "%{speed_download}\n" https://site.com` |
| `%{num_redirects}` | Número de redirecciones seguidas. | `curl -sL -o /dev/null -w "%{num_redirects}\n" http://site.com` |
| `%{redirect_url}` | URL a la que redirige (sin `-L`). | `curl -s -o /dev/null -w "%{redirect_url}\n" http://site.com` |
| `%{url_effective}` | URL final tras redirecciones. | `curl -sL -o /dev/null -w "%{url_effective}\n" http://site.com` |
| `%{remote_ip}` | IP del servidor contactado. | `curl -s -o /dev/null -w "%{remote_ip}\n" https://site.com` |
| `%{ssl_verify_result}` | Resultado de la verificación del cert (0 = OK). | `curl -s -o /dev/null -w "%{ssl_verify_result}\n" https://site.com` |
| `%{json}` | Vuelca todas las variables como objeto JSON. | `curl -s -o /dev/null -w "%{json}" https://site.com` |
| `%{header_json}` | Cabeceras de respuesta como JSON. | `curl -s -o /dev/null -w "%{header_json}" https://site.com` |
| `%header{name}` | Valor de una cabecera concreta de la respuesta. | `curl -s -o /dev/null -w "%header{server}\n" https://site.com` |

---

## Subida de ficheros (upload)

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-T`, `--upload-file <file>` | Sube un fichero local al destino (PUT en HTTP; STOR en FTP). | `curl -T local.txt ftp://site.com/remote.txt` |
| `-a`, `--append` | Anexa al fichero destino en vez de sobrescribir. | `curl -a -T log.txt ftp://site.com/log.txt` |
| `--crlf` | Convierte LF→CRLF en la subida. | `curl --crlf -T file.txt ftp://site.com/f.txt` |
| `--upload-flags <flags>` | Flags de comportamiento de subida IMAP (p. ej. `Seen`). | `curl --upload-flags Seen -T msg.eml imap://mail.com/INBOX` |

---

## FTP / FTPS

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--ftp-account <data>` | Envía datos de cuenta (comando ACCT). | `curl --ftp-account acct ftp://site.com` |
| `--ftp-alternative-to-user <cmd>` | Comando alternativo si falla `USER`. | `curl --ftp-alternative-to-user "SITE X" ftp://site.com` |
| `--ftp-create-dirs` | Crea los directorios remotos si no existen. | `curl --ftp-create-dirs -T f ftp://site.com/a/b/f` |
| `--ftp-method <method>` | Controla el uso de CWD (`multicwd`/`nocwd`/`singlecwd`). | `curl --ftp-method nocwd ftp://site.com/a/b/f` |
| `--ftp-pasv` | Usa PASV/EPSV (modo pasivo). | `curl --ftp-pasv ftp://site.com` |
| `-P`, `--ftp-port <addr>` | Modo activo: envía PORT con la dirección dada. | `curl -P - ftp://site.com` |
| `--ftp-pret` | Envía PRET antes de PASV (servidores tipo drftpd). | `curl --ftp-pret ftp://site.com` |
| `--ftp-skip-pasv-ip` | Ignora la IP que devuelve el servidor en PASV. | `curl --ftp-skip-pasv-ip ftp://site.com` |
| `--ftp-ssl-ccc` | Envía CCC (Clear Command Channel) tras autenticar. | `curl --ftp-ssl-ccc ftps://site.com` |
| `--ftp-ssl-ccc-mode <mode>` | Modo de CCC (`active`/`passive`). | `curl --ftp-ssl-ccc-mode active ftps://site.com` |
| `--ftp-ssl-control` | Exige TLS en el login, canal de datos en claro. | `curl --ftp-ssl-control ftp://site.com` |
| `--disable-eprt` | No usa EPRT/LPRT (solo PORT). | `curl --disable-eprt ftp://site.com` |
| `--disable-epsv` | No usa EPSV (solo PASV). | `curl --disable-epsv ftp://site.com` |
| `-l`, `--list-only` | Solo lista nombres (NLST en FTP; listado en otros). | `curl -l ftp://site.com/dir/` |
| `-Q`, `--quote <cmd>` | Envía comandos al servidor antes (o `-`/`+` después) de la transferencia. | `curl -Q "DELE old.txt" ftp://site.com` |
| `-B`, `--use-ascii` | Fuerza transferencia en modo ASCII/texto. | `curl -B ftp://site.com/file.txt` |

---

## SFTP / SCP / SSH

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--pubkey <key>` | Fichero de clave pública SSH. | `curl --pubkey id.pub -u user: sftp://site.com/f` |
| `--key <file>` | Fichero de clave privada SSH. | `curl --key id_ed25519 -u user: sftp://site.com/f` |
| `--hostpubmd5 <md5>` | MD5 esperado de la clave de host (verificación). | `curl --hostpubmd5 <md5> sftp://site.com` |
| `--hostpubsha256 <sha256>` | SHA256 esperado de la clave de host. | `curl --hostpubsha256 <b64> sftp://site.com` |
| `--knownhosts <file>` | Ruta del fichero `known_hosts`. | `curl --knownhosts ~/.ssh/known_hosts sftp://site.com` |
| `--compressed-ssh` | Habilita compresión SSH. | `curl --compressed-ssh sftp://site.com/f` |

Ver también [[OpenSSH]] y [[scp]] para el cliente SSH nativo.

---

## Correo — SMTP / IMAP / POP3

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--mail-from <addr>` | Remitente del sobre (MAIL FROM). | `curl --mail-from a@x.com --mail-rcpt b@y.com -T msg.eml smtp://mx` |
| `--mail-rcpt <addr>` | Destinatario del sobre (RCPT TO); repetible. | `curl --mail-rcpt b@y.com -T msg.eml smtp://mx` |
| `--mail-rcpt-allowfails` | Permite que algún RCPT TO falle sin abortar. | `curl --mail-rcpt-allowfails --mail-rcpt b@y.com ... smtp://mx` |
| `--mail-auth <addr>` | Dirección de autenticación del email original. | `curl --mail-auth a@x.com -T msg.eml smtp://mx` |
| `--login-options <opts>` | Opciones de login (p. ej. `AUTH=*`). | `curl --login-options AUTH=NTLM -u a:b imap://mail.com` |

> IMAP/POP3 usan la URL para seleccionar buzón/mensaje: `imap://mail.com/INBOX?UID 5`, `pop3://mail.com/1`. Combinar con `-X` para comandos (`-X 'FETCH 1 BODY[]'`).

---

## TFTP / TELNET / LDAP / FILE / IPFS

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--tftp-blksize <val>` | Tamaño de bloque TFTP (BLKSIZE). | `curl --tftp-blksize 1024 tftp://site.com/f` |
| `--tftp-no-options` | No envía ninguna opción TFTP. | `curl --tftp-no-options tftp://site.com/f` |
| `-t`, `--telnet-option <opt=val>` | Opción del protocolo TELNET. | `curl -t TTYPE=vt100 telnet://site.com` |
| `--ipfs-gateway <URL>` | Gateway para resolver URLs `ipfs://`. | `curl --ipfs-gateway https://ipfs.io ipfs://<cid>` |

> LDAP: `curl "ldap://ldap.site.com/dc=x,dc=com?cn?sub?(uid=admin)"`. FILE: `curl file:///etc/hostname`. DICT: `curl dict://dict.org/d:curl`.

---

## Opciones globales y de la herramienta

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-K`, `--config <file>` | Lee opciones desde un fichero de configuración. | `curl -K request.conf` |
| `-q`, `--disable` | Ignora `~/.curlrc` (debe ir primero). | `curl -q https://site.com` |
| `--variable <name=text/@file>` | Define una variable expandible con `{{name}}`. | `curl --variable host=site.com --expand-url "https://{{host}}"` |
| `-h`, `--help <subject>` | Ayuda por categorías (`curl --help all`). | `curl --help http` |
| `-M`, `--manual` | Muestra el manual completo. | `curl -M` |
| `-V`, `--version` | Versión, backend TLS, protocolos y features. | `curl -V` |
| `--dump-ca-embed` | Vuelca el bundle de CAs embebido a stdout. | `curl --dump-ca-embed > ca.pem` |

---

## Opciones heredadas (deprecated)

Presentes por compatibilidad; evitar en configuraciones nuevas.

| Opción | Descripción | Ejemplo |
|---|---|---|
| `-2`, `--sslv2` | Fuerza SSLv2 (inseguro, casi siempre deshabilitado). | `curl -2 https://old.com` |
| `-3`, `--sslv3` | Fuerza SSLv3 (inseguro). | `curl -3 https://old.com` |
| `--ntlm-wb` | NTLM con winbind (obsoleto). | `curl --ntlm-wb -u user https://site.com` |
| `--metalink` | Procesa la URL como XML metalink. | `curl --metalink https://site.com/f.metalink` |
| `--egd-file <file>` | Ruta del socket EGD para entropía (ignorado en backends modernos). | `curl --egd-file /var/run/egd https://site.com` |
| `--random-file <file>` | Fichero fuente de datos aleatorios (ignorado). | `curl --random-file /dev/urandom https://site.com` |

---

## Protips

### Checkear IP pública
```bash
curl ifconfig.me
```

### Fecha exacta del servidor (en GMT)
```bash
curl -sI google.com | grep -w "Date:" | cut -d' ' -f2-
```

### Desglose de tiempos de una petición (diagnóstico de latencia)
```bash
curl -w "dns:%{time_namelookup} tcp:%{time_connect} tls:%{time_appconnect} ttfb:%{time_starttransfer} total:%{time_total}\n" -o /dev/null -s https://site.com
```

### Solo el código de estado HTTP
```bash
curl -o /dev/null -s -w "%{http_code}\n" https://site.com
```

### Reproducir una petición del navegador vía Burp
```bash
curl -x http://127.0.0.1:8080 -k https://target.com --compressed
```

### Fuzzing manual de rutas con globbing
```bash
curl -s -o /dev/null -w "%{http_code} %{url_effective}\n" "https://target.com/{admin,backup,config,.git}"
```

### Acceso a la API de Docker vía socket Unix
```bash
curl --unix-socket /var/run/docker.sock http://localhost/containers/json
```

### Convertir un comando en código libcurl (C)
```bash
curl --libcurl req.c -d "a=1" https://api.com
```
