
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