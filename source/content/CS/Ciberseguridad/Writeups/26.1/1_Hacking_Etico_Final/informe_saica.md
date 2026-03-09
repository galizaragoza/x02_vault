# Informe
## Dominios
A partir de los resultados en diversos motores de búsqueda como Bing, Google, DuckDuckGo, Shodan y Yandex. Utilizando en las búsquedas distintos operadores (`site:`, `inurl:`, `intext:`, `intitle:`...) Se elabora una tabla con todos los dominios encontrados, tiene con los siguientes campos.

| **ID**                                                       | **Dominio** | **IP**      | **Hipótesis**                       | **Notas adicionales**                      |
| ------------------------------------------------------------ | ----------- | ----------- | ----------------------------------- | ------------------------------------------ |
| Identificador para referenciar en otros dominios de la tabla | Dominio     | IP asociada | Conjeturas sobre el uso del dominio | Observaciones adicionales sobre el dominio |

|     | **Dominio**                | **IP asociada**                | **Uso (probable)**                                                                   | **Notas**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| --- | -------------------------- | ------------------------------ | ------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0   | saica.com                  | 194.179.125.161                | Dominio principal, actúa como landing page (contacto, info, servicios, productos...) | Madrid, ISP Telefónica, puertos 80 y 443 abiertos                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|     | **Subominio**              | **IP asociada**                | **Uso (probable)**                                                                   | **Notas**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 1   | saicanet.saica.com         | 194.179.125.132<br><br>        | Servicio para uso interno                                                            | Tras un reverse proxy f5, servidor BigIP                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 2   | compliance.saica.com       | 194.179.125.132                | Página para quejas/denuncias internas.                                               | Permite envío anónimo, sin pruebas y desde cualquier país/base                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 3   | apps.saica.com             | 194.179.125.132                |                                                                                      | Da 403 (no hay permi1so)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 4   | saica.benefitsatwork.es    | 81.17.209.154                  | Parece una especie de página para canjear premios y otros beneficios                 | Subdominio de Saica de una consultoría Alemana                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 5   | adgsfs.saica.com           | 194.179.125.173                | Panel de acceso a sistema interno                                                    | Aparentemente es una máquina Microsoft, accesible solo en este [enlace concreto](https://adgsfs.saica.com/adfs/ls/?client-request-id=8916d211-ad42-68be-790c-3bd8bd814cf8&username=&wa=wsignin1.0&wtrealm=urn%3Afederation%3AMicrosoftOnline&wctx=estsredirect%3D2%26estsrequest%3DrQQIARAAjZE7bNNQAEX94sQkUCAwIMaqMIEc_5-dIAYncUrJp_m0TdolOI5f7AbnubZDPhtISFQsnTvCFomFqVRqYWLoFJgQE2OFRIWYMtKIhbF3OLrS3c69R3IJLnWX_ReenpNmEeJow5y3_-LdvBy_8fXWbvrdsTVaePDt6Hlhtg9ivm4besLAzgQsWUHg-imGwf3gKcbdBEbINkwBSvOdwQOdOQBgCsApAJOQDAU5KcOkyELIyVDiBTGBFMHkEW_QUBEFWpRYk24ZHEfDFlJEGbZbumx-D11fVfuBxc-BPXts_gnFEPacpov9YJ98AbJGUMpgtaNpacur99nWDpKKirC5su0VdNVNa9W1brJRHj5r82sVbNOw6rr1Ts1yxY2BulnCmTrqVbZQxx_5_Xxl3MkZA2dHsv2NqlVQiyvJotPYEpxlmGebrW7TUTjXLA-zXptrbgc6FLj0YEJeSOl7kjoX4-DeCUlh1-zZ7WkY_AiDn-EFFqSiUSpO3AaLxCwM3kTO7X9-MovY64vLR-jO27MvEnESYXJGdlBbva81RlZ-xI-RWteQPXrMtplco9yooJzdwVq2VuoN1x8qKW6PAnsUdUjFomScWCIzZe6UAr8p8OoScRi7wH3TK-DTVWJ27Xj39cuDDx9_PfoL0). |
|     | admsfs.saica.com           | 194.179.125.140                | Panel de acceso a sistema interno                                                    | Similar a 5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 6   | usa.saica.com              | 194.179.125.132                | Página principal, versión EEUU                                                       | Alojada en el mismo servidor que 2 y 3.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 7   | vacancies.saica.com        | 54.247.128.77<br>34.252.163.99 | Página de vacantes a cubrir.                                                         | Servidores de AWS (rotación frecuente probable), gestionados por la consultoría de HR Bizneo (probable más servicios externalizados a parte de la contratación)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 8   | qseportal.saica.com        | 194.179.125.132                | Sistema de gestión de seguridad laboral prosafety                                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 9   | b2bpaper.saica.com         | 194.179.125.132                | Panel de acceso para negocios                                                        | Panel de control para negocios, pedidos al por mayor(probable)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 10  | b2bpapertest.ext.saica.com | 194.179.125.143                | Panel de acceso para negocios                                                        | Servidor distinto que 9, sufijo .ext que siguiere externo (?), BigIP server                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 11  | packb2b.saica.com          | 13.80.45.58                    | Panel de acceso para negocios (servicio Saica Pack)                                  | Servidor neerlandés, alojado en Azure, gestionado/hecho por SAP (software gestión empresas)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 12  | campusonline.saica.com     | 52.178.145.71                  | Plataforma de formación interna                                                      | Servicio SSH expuesto en el puerto 2222, alojado en Azure en Dublin                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 13  | graduate.saica.com         | 54.247.128.77                  | Plataforma de ofertas para recién graudados                                          | Mismo servidor que 7, también gestionada por Bizneo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 14  | naturlca.saica.com         | 167.99.19.207                  |                                                                                      | SSH expuesto, tres vulnerabilidades listadas por Shodan, Nginx en Ubuntu. CP DigitalOcean.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| 15  | maximowas.saica.com        | 194.179.125.132                |                                                                                      | Login HTTPAuth                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 16  | fundacion.saica.com        | 194.179.125.132                | Página de la fundación Saica                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 17  | 2025.saica.com             | 194.179.125.132                |                                                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 18  | saica.puntes.es            | 18.100.3.222                   | Panel de gestión con socio comercial                                                 | Puntes es un proveedor de packaging, servidor AWS en Zaragoza                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 19  | mypassword.saica.com       | 194.179.125.132                | Funcionalidad de recuperación de contraseñas                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
<div class="page-break" style="page-break-before: always;"></div>


## Diagrama y análisis de la infraestructura
Analizando la lista se cruzan fuentes de las herramientas `whois`, `nslookup` y el motor de búsqueda Shodan, y se elabora el gráfico presente más abajo.

Se identifica con la información obtenida que varios de los dominios de Saica están alojados en servidores de distintos cloud providers (AWS y Azure) y están registrados a nombre de otras empresas. De esto se deduce que esos servidores/dominios no forman parte de la infraestructura de Saica y son más bien servicios que le se le proveen a esta (mayormente RRHH) o bien paneles de control para interactuar con socios comerciales.

Sin embargo, todas las IPs que si aparecen como registradas a nombre de Saica se encuentran dentro de un rango: 194.179.125.0/24. De esta información se asume que toda la infraestructura web de Saica (o al menos la accesible desde Internet) se encuentra en una misma subnet.

![[saica.jpg]]
<div class="page-break" style="page-break-before: always;"></div>

Al hacer un `whois` en el rango 194.179.125.0/24 nos confirma lo que ya sabemos, el ISP es telefónica. Inspeccionando una por una las IPs combinando `ping` y `whois`, se observa que tienen un rango de IPs en este servidor que va desde 194.179.125.128 hasta 194.179.125.191, de modo que la subnet de Saica es realmente 194.179.125.128/26. Casi con total seguridad se trata de una subnet virtual contenida en algún servicio de cloud de Telefónica.

Muchas de las IPs no son accesibles, sugiriendo firewalls perimetrales, analizándolas una por una nos queda la siguiente lista que merezca la pena investigar más a fondo.

Lista deducida a base de un script que lanza un `ping` a todas las IPs en el rango 194.179.125.128/26 y evalúa si responden o no:

```
194.179.125.130, 194.179.125.132, 194.179.125.143, 194.179.125.144, 194.179.125.152, 194.179.125.154, 194.179.125.155, 194.179.125.156, 194.179.125.157, 194.179.125.158, 194.179.125.160, 194.179.125.161, 194.179.125.165, 194.179.125.166, 194.179.125.167, 194.179.125.168, 194.179.125.169, 194.179.125.174, 194.179.125.175, 194.179.125.177, 194.179.125.178, 194.179.125.186, 194.179.125.187, 194.179.125.188, 194.179.125.191
```
<div class="page-break" style="page-break-before: always;"></div>

## Análisis exhaustivo de las IPs expuestas
Se desarrolla un script para realizar un escaneo vía `nmap` de cada una de las IPs recopiladas para un análisis más profundo. [Código fuente](https://github.com/galizaragoza/sec_tools/blob/main/nmapper.sh).

Se analizan los resultados de las distintas IPs uno por uno y se filtra la información relevante. Los siguientes apartados analizando las IPs uno por uno tienen la siguiente información.

- Captura de pantalla del escaneo generado por Nmap
- Hipótesis del propósito del servidor
- Información sobre los servicios
- CVEs (de haberlas)


> Varias de las IPs en el diagrama son inaccesibles con peticiones directas a la IP a la que están asociadas, en concreto 194.179.125.140, 194.179.125.143 y 194.179.125.161. Están fuera de la lista ya que no se pudo averiguar información relevante sobre ellas desde Nmap, curl o Shodan con distintos métodos, se asume que F5 BigIP las proteje tras un Firewall, ya que saica.com es perfectamente accesible pero 194.179.125.161 (su IP) no.
<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.130 / mail-out4.saica.com
![[194.179.125.130.png]]

A juzgar por el dominio asociado a esta IP, podría tratarse de un servidor relacionado con mailing. Basándome en la información de los puertos es una teoría con bastante sentido, y se respalda en la presencia de un firewall cuyo propósito sea probablemente filtrar el tráfico de mail saliente (por la parte `-out` del dominio).
- El puerto 264 está corriendo un Firewall, [CheckPoint](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/SASE-Admin-Guide/Content/Topics-SASE-IPsec-VPN/On-premises/CheckPoint.htm) es un vendedor de software y en el caso de este servidor ambos servicios son de este proveedor. Investigando sobre el producto señalado por nmap se descubre que FireWall-1 se trata de una versión concreta de este proveedor.
- El puerto 18264 aloja un servicio del mismo vendedor, en este caso un SVN (un software de control de versiones OpenSource hecho por Apache), que tras leer un poco la documentación de CheckPoint deduzco que es consumido por el propio software de CheckPoint, quizá para descargar actualizaciones.

> El mismo servicio en el mismo puerto corre también en la IP 194.179.125.177

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.132
![[194.179.125.132.png]]

Esta IP aloja servicios de load balancing y varias páginas de administración internas, es la IP detrás de la mayoría de los dominios en el diagrama [[informe_saica#Diagrama y análisis de la infraestructura|infraestructura]], así que casi con total seguridad este servidor contiene o bien virtual hosts, contenedores o VMs a las que se enruta el tráfico en función de la dirección solicitada.
- El puerto 80 actúa como load balancer, del vendedor f5, podría estar gestionado por telefónica, ya que el nombre del dominio sugiere que aquí se definen ciertos parámetros en la infraestructura del telefónica sobre como se aprovisionan los recursos a Saica, los dos load balancers en los puertos 80 y 443 podrían ser lo que enruta el tráfico por todo el resto de la subnet de Saica.
- En el puerto 443 hay un servicio muy similar al del puerto 80, solo que con un certificado SSL. F5 tiene un servicio de Load Balancing y DNS así que se asume que Saica consume este servicio para load balancing, DNS y firewall.
- En el puerto 1080 hay un panel de login a una interfaz de control interna de Node-RED (una herramienta de desarrollo basada en flujo para programación visual desarrollada originalmente por IBM para conectar dispositivos de hardware, API y servicios en línea como parte de IoT). Esto respalda la teoría de que esta IP es de la configuración de la nube de Saica ya que esto podría servir para configurar dispositivos virtuales. O bien podría ser algo interno de Saica para monitorizar su infraestructura IoT.
- El puerto 8088 aloja un panel de administración de activos. Está corriendo en Ubuntu con Nginx 1.14.0 (Apr. 2018, vulnerable) y el título HTTP es com.kyubi.warehouse.inventory.webapp.ui. Kyuubi es un producto de Apache para gestionar inventarios de almacenes.

Tabla con CVEs de Nginx 1.14.0:

| **CVE**        | **CVSS** | **NIST Link**                                   |
| -------------- | -------- | ----------------------------------------------- |
| CVE-2018-16845 | 8.2      | https://nvd.nist.gov/vuln/detail/cve-2018-16845 |
| CVE-2018-16843 | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2018-16843 |
| CVE-2018-16844 | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2018-16844 |
| CVE-2019-9511  | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2019-9511  |
| CVE-2019-9513  | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2019-9513  |
| CVE-2021-23017 | 7.7      | https://nvd.nist.gov/vuln/detail/cve-2021-23017 |
| CVE-2019-9516  | 7.5      | https://nvd.nist.gov/vuln/detail/cve-2019-9516  |
| CVE-2023-44487 | 7.5      | https://nvd.nist.gov/vuln/detail/cve-2023-44487 |
| CVE-2021-3618  | 7.4      | https://nvd.nist.gov/vuln/detail/cve-2021-3618  |
| CVE-2024-7347  | 5.7      | https://nvd.nist.gov/vuln/detail/cve-2024-7347  |
| CVE-2019-20372 | 5.3      | https://nvd.nist.gov/vuln/detail/cve-2019-20372 |
| CVE-2025-23419 | 5.3      | https://nvd.nist.gov/vuln/detail/cve-2025-23419 |

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.144
![[194.179.125.144.png]]

- Puerto 2020 servidor de control XINU (SO para sistemas embebidos), probablemente de control o monitorización para IoT o controladores industriales
- Puerto 8443 protegido por firewall solo responde con tcpwrapped (un firewall esta protegiendo la aplicación tras el puerto). Al probar con un un escaneo menos intrusivo (`nmap -p 8443 -T3 -sT -v -Pn 194.179.125.144`), nmap saca un servicio llamado `https-alt`, pero al tratar de hacer peticiones vía `curl` el firewall bloquea de nuevo(`curl: (56) Recv failure: Connection reset by peer`), por lo que se asume que hay una whitelist en la que no estamos.

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.152, 168, 169
![[194.179.125.152.png]]

- En el puerto 443 tenemos un servidor HTTPS corriendo en Apache 2.4.37 (Oct. 2018, vulnerable).

CVEs de Apache 2.4.37

| **CVE**        | **CVSS** | **NIST Link**                                   |
| -------------- | -------- | ----------------------------------------------- |
| CVE-2020-11984 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2020-11984 |
| CVE-2021-26691 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2021-26691 |
| CVE-2021-39275 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2021-39275 |
| CVE-2021-44790 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2021-44790 |
| CVE-2022-22720 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2022-22720 |
| CVE-2022-23943 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2022-23943 |
| CVE-2022-31813 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2022-31813 |
| CVE-2023-25690 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2023-25690 |
| CVE-2024-38474 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2024-38474 |
| CVE-2024-38476 | 9.8      | https://nvd.nist.gov/vuln/detail/cve-2024-38476 |
| CVE-2019-10082 | 9.1      | https://nvd.nist.gov/vuln/detail/cve-2019-10082 |
| CVE-2022-22721 | 9.1      | https://nvd.nist.gov/vuln/detail/cve-2022-22721 |
| CVE-2022-28615 | 9.1      | https://nvd.nist.gov/vuln/detail/cve-2022-28615 |
| CVE-2024-38475 | 9.1      | https://nvd.nist.gov/vuln/detail/cve-2024-38475 |
| CVE-2024-40898 | 9.1      | https://nvd.nist.gov/vuln/detail/cve-2024-40898 |
| CVE-2025-23048 | 9.1      | https://nvd.nist.gov/vuln/detail/cve-2025-23048 |
| CVE-2021-40438 | 9.0      | https://nvd.nist.gov/vuln/detail/cve-2021-40438 |
| CVE-2022-36760 | 9.0      | https://nvd.nist.gov/vuln/detail/cve-2022-36760 |
| CVE-2025-58098 | 8.3      | https://nvd.nist.gov/vuln/detail/cve-2025-58098 |
| CVE-2021-44224 | 8.2      | https://nvd.nist.gov/vuln/detail/cve-2021-44224 |
| CVE-2024-38473 | 8.1      | https://nvd.nist.gov/vuln/detail/cve-2024-38473 |
| CVE-2019-0211  | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2019-0211  |
| CVE-2019-9517  | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2019-9517  |
| CVE-2006-20001 | 7.5      | https://nvd.nist.gov/vuln/detail/cve-2006-20001 |

> Se han incluido las IPs 168 y 169 por tener patrones idénticos o muy similares

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.154
![[194.179.125.154.png|873x542]]

De nuevo, tanto el contenido del puerto 80 como el 443 está tras el producto BigIP, que parece estar actuando también como firewall además de load balancer, ya que bloquea con este mensaje

![[bigIP_firewall.png|248x328]]

Dando a entender que hay una whitelist o un paso previo que da credenciales de las que carezco. Se asume que todos los servidores tras esta protección son interfaces internas de gestión ya que además no tienen dominio asociado.

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.155
![[194.179.125.155.png]]

De nuevo el puerto 80 está protegido por BigIP, en el 443 está corriendo el servicio HTTPAPI, lo que significa que la máquina tras él es casi con total seguridad Windows, pero la versión no es vulnerable. Según nmap está corriendo UPnP, un protocol de red para oficinas pequeñas.

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.156
![[194.179.125.156.png|963x557]]

En los puertos 25 y 110, dos servidores de mailing (SMTP y POP3) corriendo en Windows desactualizado, no se consigue fingerprintear el puerto 25, pero si el 110 con este comando: `nmap -p 25,110 -sTV -T2 194.179.125.156`. Resulta ser una version vulnerable a [cve-2020-0688](https://nvd.nist.gov/vuln/detail/cve-2020-0688).
Por otro lado, el puerto 443 está bloqueado tras el firewall de nuevo.

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.160
![[194.179.125.160.png]]

En esta máquina solo hay expuesto un servidor SSH en el puerto 22, en concreto, la versión 9.2 para Windows, Nmap también es capaz de averiguar con alta precisión la versión del OS (Windows Server 2019), que tiene 4066 vulnerabilidades que se pueden consultar en [este enlace](https://www.cvedetails.com/vulnerability-list/vendor_id-26/product_id-50662/Microsoft-Windows-Server-2019.html?page=1&order=3), las cuales no listo aquí debido a la imposibilidad de verificar con un mínimo de rigor. Si están en la tabla de abajo, sin embargo, las vulnerabilidades públicas de esta versión de OpenSSH.

| **CVE**        | **CVSS** | **NIST Link**                                   |
| -------------- | -------- | ----------------------------------------------- |
| CVE-2024-6387  | 8.1      | https://nvd.nist.gov/vuln/detail/cve-2024-6387  |
| CVE-2024-39894 | 7.5      | https://nvd.nist.gov/vuln/detail/cve-2024-39894 |
| CVE-2025-26465 | 6.8      | https://nvd.nist.gov/vuln/detail/cve-2025-26465 |
| CVE-2023-51385 | 6.5      | https://nvd.nist.gov/vuln/detail/cve-2023-51385 |
| CVE-2023-48795 | 5.9      | https://nvd.nist.gov/vuln/detail/cve-2023-48795 |
| CVE-2025-26466 | 5.9      | https://nvd.nist.gov/vuln/detail/cve-2025-26466 |
| CVE-2023-51384 | 5.5      | https://nvd.nist.gov/vuln/detail/cve-2023-51384 |
| CVE-2025-32728 | 4.3      | https://nvd.nist.gov/vuln/detail/cve-2025-32728 |
| CVE-2025-61984 | 3.6      | https://nvd.nist.gov/vuln/detail/cve-2025-61984 |
| CVE-2025-61985 | 3.6      | https://nvd.nist.gov/vuln/detail/cve-2025-61985 |

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.165, 167, 168
![[194.179.125.165.png]]

- 443: Servidor HTTPS inaccesible
- 4172: Servicio PCoIP (control remoto de máquinas físicas y virtuales)
- 8443: Otro servidor HTTPS inaccesible, dado que responde con `Missing Host header` se prueba a mandar una request con headers como `Host: localhost`, `X-Forwarded-For: 127.0.0.1` y `X-Originating-IP: 127.0.0.1` pero en ninguno responde, se asume firewall.

> Se han incluido las direcciones 167 y 168 en este apartado ya que tienen patrones idénticos o muy similares

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.174
![[194.179.125.174.png]]

- 5061: Servicio SIP (Session Initiation Protocol), por el contexto corporativo/industrial y el producto (Tandberg-4145 VoIP server), se trata evidentemente de un servidor VoIP (Voice over IP), se trata de un servicio de Cisco para videoconferencias. La versión, X14.2.5, es vulnerable

| **CVE**        | **CVSS** | **NIST Link**                                   |
| -------------- | -------- | ----------------------------------------------- |
| CVE-2024-20252 | 9.6      | https://nvd.nist.gov/vuln/detail/cve-2024-20252 |
| CVE-2024-20254 | 9.6      | https://nvd.nist.gov/vuln/detail/cve-2024-20254 |
| CVE-2024-20255 | 8.2      | https://nvd.nist.gov/vuln/detail/cve-2024-20255 |
| CVE-2023-44487 | 7.5      | https://nvd.nist.gov/vuln/detail/cve-2023-44487 |
| CVE-2024-20492 | 6.7      | https://nvd.nist.gov/vuln/detail/cve-2024-20492 |
| CVE-2024-20497 | 4.3      | https://nvd.nist.gov/vuln/detail/cve-2024-20497 |
- 5062: El servicio encontrado por Nmap es na-localise, sobre el que no hay mucha información, por lo leído en Internet parece ser un proceso auxiliar a el servidor VoIP

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.175
![[194.179.125.174 1.png]]

Esta IP tiene un puerto 5061 idéntico al anterior, con la diferencia del puerto 8443, que aloja un servidor HTTP asociado al servicio Cisco Expressway E, que es el nombre de la serie de productos entre los que se encuentra el Tandberg-4145, probablemente se trata de una interfaz web de gestión de dicho servicio.

<div class="page-break" style="page-break-before: always;"></div>

### 194.179.125.186, 187
![[194.179.125.187.png]]

En esta IP tenemos al culpable del problema mencionado en la nota del inicio de la sección, ya casi con total seguridad es lo que estaba bloqueando la inspección más minuciosa y el acceso mediante la IP a muchos recursos de la red interna de Saica. El producto en cuestión es potencialmente vulnerable (no se pudo averiguar la versión, así que esto no es 100% seguro), basándome en las versiones de otros servicios de la infraestructura (~2018), es muy probable que algunas de las vulnerabilidades más graves sean explotables.

| **CVE**        | **CVSS** | **NIST Link**                                   |
| -------------- | -------- | ----------------------------------------------- |
| CVE-2018-0101  | 10.0     | https://nvd.nist.gov/vuln/detail/cve-2018-0101  |
| CVE-2019-1713  | 9.3      | https://nvd.nist.gov/vuln/detail/cve-2019-1713  |
| CVE-2018-0228  | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2018-0228  |
| CVE-2018-15388 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2018-15388 |
| CVE-2019-1694  | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2019-1694  |
| CVE-2019-15256 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2019-15256 |
| CVE-2024-20260 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2024-20260 |
| CVE-2025-20133 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2025-20133 |
| CVE-2025-20134 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2025-20134 |
| CVE-2025-20136 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2025-20136 |
| CVE-2025-20239 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2025-20239 |
| CVE-2025-20243 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2025-20243 |
| CVE-2025-20253 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2025-20253 |
| CVE-2025-20263 | 8.6      | https://nvd.nist.gov/vuln/detail/cve-2025-20263 |
| CVE-2025-20251 | 8.5      | https://nvd.nist.gov/vuln/detail/cve-2025-20251 |
| CVE-2018-15465 | 8.1      | https://nvd.nist.gov/vuln/detail/cve-2018-15465 |
| CVE-2011-0379  | 7.9      | https://nvd.nist.gov/vuln/detail/cve-2011-0379  |
| CVE-2019-1687  | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2019-1687  |
| CVE-2019-1697  | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2019-1697  |
| CVE-2019-1945  | 7.8      | https://nvd.nist.gov/vuln/detail/cve-2019-1945  |
| CVE-2019-1693  | 7.7      | https://nvd.nist.gov/vuln/detail/cve-2019-1693  |
| CVE-2019-12677 | 7.7      | https://nvd.nist.gov/vuln/detail/cve-2019-12677 |
| CVE-2025-20244 | 7.7      | https://nvd.nist.gov/vuln/detail/cve-2025-20244 |
| CVE-2018-0296  | 7.5      | https://nvd.nist.gov/vuln/detail/cve-2018-0296  |
| CVE-2022-20866 | 7.5      | https://nvd.nist.gov/vuln/detail/cve-2022-20866 |
<div class="page-break" style="page-break-before: always;"></div>


# Mitigación
Debido a la imposibilidad de incluir demostraciones y comprobar con rigor si los CVEs listados son realmente explotables por falta de consentimiento legal, es imposible generar un apartado de mitigación adecuado.

Por esta razón esta sección es breve y contiene únicamente algunos consejos generales.

## Parchear el software
Se recomienda actualizar lo antes posible las versiones antiguas del software a los últimos parches de seguridad, preferiblemente a versiones LTS(Long Term Support), es especialmente crítico actualizar los siguiente productos. 
### Servidor Nginx
El servidor de Nginx que está corriendo en la IP 194.179.125.132 está en la versión 1.14.0, las vulnerabilidades están listadas en la [[informe_saica#194.179.125.132|sección de la IP mencionada]].
Aquí hay un [artículo](https://developerinsider.co/install-update-nginx-to-the-latest-stable-version-on-ubuntu/) que cubre como actualizar a la última versión.

### Apache
En las IPs 194.179.125.152, 194.179.125.168 y 194.179.125.169 corre una versión vulnerable de Apache, con sus vulnerabilidades listadas en su [[informe_saica#194.179.125.152, 168, 169|sección]]
- [Consejos generales para actualizar apache](https://cyrisk.com/security/general-instructions-for-upgrading-an-apache-http-server-to-the-latest-version/) (2.4.66)

### OpenSSH
La versión 9.5 para Windows de OpenSSH en la IP 194.179.125.160 es vulnerable, sus vulnerabilidades están listadas en su [[informe_saica#194.179.125.160|sección]].
- [Última versión de OpenSSH para Windows](https://github.com/PowerShell/openssh-portable/releases/tag/v10.0.0.0) (10.0.0)

### Revisar firmware CISCO ASA Firewall
No se pudo averiguar la versión del software para este producto, solo se hizo una conjetura en base a otras versiones de productos en la infraestructura, sería conveniente revisar la versión vigente y actualizar si es necesario.


## Dificultar el fingerprinting
Sería ideal dificultar el fingerprinting de las distintas tecnologías y servicios, con métodos como los listados en este [artículo de OWASP](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/02-Fingerprint_Web_Server).
Estas medidas, como no filtrar headers o configurar los servicios tras un proxy, dificulta la tarea de los actores maliciosos de averiguar con precisión los componentes de la infraestructura de la empresa.