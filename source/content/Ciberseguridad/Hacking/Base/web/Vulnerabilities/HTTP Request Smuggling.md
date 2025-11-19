### ¿Qué es?
 El HTTP Request Smuggling es una técnica para interferir en la manera en que una página web interpreta las secuencias de peticiones de uno o varios usuarios, esta vulnerabilidad es a menudo crítica ya que permite burlar controles de seguridad, ganar acceso no autorizado a información sensible y comprometer directamente las aplicaciones de otros usuarios.
Esta vulnerabilidad está **principalmente asociada con peticiones HTTP/1**, aunque puede llegar a darse en HTTP/2 dependiendo de la estructura del back-end de la víctima.
### Recursos
[Smuggling tester CLI tool](https://github.com/defparam/smuggler)
### Artículos
[albinowax - HTTP Desync Attacks: Smashing into the Cell Next Door - DEF CON 27 Conference ](https://youtu.be/w-eJM2Pc0KI)
[Artículo de PortSwigger al respecto](https://portswigger.net/web-security/request-smuggling#what-is-http-request-smuggling)
[A pentesters guide to HTTP Request Smuggling](https://www.cobalt.io/blog/a-pentesters-guide-to-http-request-smuggling)
[CVE-2022-29361](https://nvd.nist.gov/vuln/detail/CVE-2022-29361)
> CVE-2022-29361 es una vulnerabilidad de Werkzeug v2.1.0 hacia abajo que permite la ejecución de Request Smuggling mediante peticiones manualmente creadas. 