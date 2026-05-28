# Introducción
En este documento se explica detalladamente un ejercicio de penetración ofensiva llevado a cabo sobre la start-up AyudaPyme, en este documento se explica tanto en un resumen ejecutivo (no técnico), como en más detalle en la sección técnica todo el proceso de las pruebas, desde el reconocimiento de la infraestructura y la recopilación de información de fuentes abiertas hasta los entresijos descubiertos del funcionamiento de la aplicación

<div class="page-break" style="page-break-before: always;"></div>

# Índice

- ### [[AyudaPyme_report#Introducción|Introducción]]
- ### [[AyudaPyme_report#Índice|Índice]]
- ### [[AyudaPyme_report#Introducción|Metodología]]
- ### [[AyudaPyme_report#Resumen ejecutivo|Resumen ejecutivo]]
- ### [[AyudaPyme_report#Resumen técnico|Resumen técnico]]
- ### [[AyudaPyme_report#Informe|Informe]]
	- #### [[AyudaPyme_report#Servidor|Servidor]]
	- #### [[AyudaPyme_report#Información de fuentes abiertas|Información de fuentes abiertas]]
	- #### [[AyudaPyme_report#Formulario de registro|Formulario de registro]]
- ### [[AyudaPyme_report#Mitigación / prevención|Mitigación / prevención ]]
	- #### [[AyudaPyme_report#WEB|WEB]]
	- #### [[AyudaPyme_report#RE|RE]]
	- #### [[AyudaPyme_report#MS|MS]]

<div class="page-break" style="page-break-before: always;"></div>

# Metodología
En este ejercicio el objetivo es identificar todas las potenciales brechas de seguridad presentes en la aplicación web. Se pretenden identificar dichas vulnerabilidades siguiendo una metodología consistente en varias fases: Reconocimiento, identificación, explotación (de ser posible). Es decir, en primer lugar se analiza la infraestructura, para después identificar los activos que la componen, una vez hecho esto, se intenta vulnerar la seguridad de dichos activos para demostrar las brechas que han de ser corregidas.

El reporte, dividido en distintas secciones, contiene un resumen de la vulnerabilidad hallada tras un código para referencia interna(en el propio documento), además del CWE asociado (el CWE es un código estándar en la industria para identificar debilidades habituales). Al final del documento, se puede encontrar también la sección correspondiente de mitigación y prevención. En la que se dan indicaciones para corregir las debilidades encontradas. Respecto al hallazgo de las debilidades en sí, se ha tratado de documentar con rigor técnico los pasos seguidos, con imágenes y texto de la terminal, de manera que los pasos puedan reproducirse.

La estructura general del documento es la siguiente, a partir de la sección bajo esta misma:
 - Resumen **ejecutivo**: Resumen no-técnico
 - Resumen **técnico**: Contiene un índice de debilidades en formato tabla.
 - **Informe**: El informe en sí, dividido en secciones (servidor, información OSINT, formulario de registro)
 - Mitigación y **prevención**: Una serie de contramedidas para corregir las debilidades descubiertas.

<div class="page-break" style="page-break-before: always;"></div>

# Resumen ejecutivo
Durante el ejercicio de las pruebas se encuentra un número relativamente alto de vulnerabilidades, sobre todo teniendo en cuenta el reducido tamaño de la infraestructura de la empresa. Muchas de ellas son de gravedad baja y consisten en malas prácticas de desarrollo, no tanto en vulnerabilidades graves.

Esto se debe en parte a la limitada cantidad de puntos de entrada de la aplicación, que se reduce únicamente al formulario de registro para autónomos y empresas presente en la web, debido a esto la mayoría de vulnerabilidades reportadas están basadas en malas prácticas, fallos de configuración y errores en la arquitectura de la infraestructura, o en como se procesan los datos.

Durante el ejercicio se identifican problemas en la falta de algunas medidas de seguridad estándar en el servidor, la exposición pública de alguna información sensible de la infraestructura y en como la aplicación se comunica procesa la información que recibe el formulario mencionado, exponiendo lógica sensible de los flujos.
De todas formas, ninguna de las vulnerabilidades ha resultado ser crítica en el sentido de que suponga un peligro inminente para la empresa, y el reporte reúne más bien una serie de cosas a mejorar, no tanto de problemas graves que urja corregir. Esto es algo natural teniendo en cuenta el tamaño y falta de experiencia del equipo técnico.


<div class="page-break" style="page-break-before: always;"></div>

# Resumen técnico
Este sección contiene una tabla con un resumen técnico de los hallazgos encontrados durante el ejercicio de las pruebas, la tabla contiene la información siguiente con la distribución
- **Código**: Un identificador interno de este documento para referenciar la vulnerabilidad en cuestión (RE: Reconocimiento; WEB: Servidor; MS: Sitio web)
- **Gravedad**: Una etiqueta evaluando la gravedad del hallazgo (informativo, leve, medio, grave, crítico)
- **Síntesis**: Breve explicación de la vulnerabilidad
- **CWE**: Código del estándar [Common Weakness Enumeration](https://cwe.mitre.org/data/definitions/200.html)
- Enlace a la **demostración** de la vulnerabilidad en el documento
- Enlace a las buenas prácticas y medidas de **mitigación/prevención** en el documento

| Código (ID) | Gravedad    | Síntesis                                    | CWE                                                        |
| ----------- | ----------- | ------------------------------------------- | ---------------------------------------------------------- |
| RE-1        | Media       | Repositorio público                         | [CWE-200](https://cwe.mitre.org/data/definitions/200.html) |
| RE-2        | Media       | Exposición de endpoint sensible             | [CWE-200](https://cwe.mitre.org/data/definitions/200.html) |
| WEB-1       | Leve        | Demasiada info. en cabeceras HTTP           | [CWE-200](https://cwe.mitre.org/data/definitions/200.html) |
| WEB-2       | Media       | Falta la cabecera Strict-Transport-Security | [CWE-523](https://cwe.mitre.org/data/definitions/523.html) |
| WEB-3       | Leve        | Falta la cabecera X-Content-Type-Options    | [CWE-644](https://cwe.mitre.org/data/definitions/644.html) |
| WEB-4       | Alta        | Falta de rate-limiting                      | [CWE-799](https://cwe.mitre.org/data/definitions/799.html) |
| MS-1        | Alta        | Falta de validación de input                | [CWE-20](https://cwe.mitre.org/data/definitions/20.html)   |
| MS-2        | Media       | Exposición de información sensible          | [CWE-200](https://cwe.mitre.org/data/definitions/200.html) |
| MS-3        | Informativo | Criptografía potencialmente insuficiente    | [CWE-5](https://cwe.mitre.org/data/definitions/5.html)     |


<div class="page-break" style="page-break-before: always;"></div>

# Informe
## Servidor
### WEB-1: Demasiada info. en cabeceras HTTP
Analizando las respuestas del servidor, se identifica un exceso de información proporcionada a potenciales adversarios en las cabeceras HTTP, de una simple petición a la raíz del servidor, se puede identificar el proveedor de cloud, CDN. La respuesta se saca, sencillamente, de la herramienta de desarrollador del navegador. Esto es un problema porque da todo el trabajo de reconocimiento hecho, cuanta menos información se le de a potenciales adversarios sobre las tecnologías subyacentes a una web, mejor.

```
HTTP/2 304 
server: railway-edge
x-railway-edge: railway/europe-west4-drams3a
x-railway-request-id: 3ftAaX64SWmjnHkYALIuDA
accept-ranges: bytes
date: Mon, 02 Mar 2026 12:05:06 GMT
via: 1.1 varnish
x-cache: MISS
x-cache-hits: 0
x-timer: S1772453106.731487,VS0,VE491
x-railway-cdn-edge: fastly/cache-mad2200116-MAD
X-Firefox-Spdy: h2
```

Además, en el análisis de esta respuesta del servidor de identificar otras vulnerabilidades.

### WEB-2: Falta la cabecera Strict-Transport-Security  
La cabecera Strict-Transport-Security no está definida, esto significa que a pesar de que el servidor permite tráfico HTTPS y utiliza TLS, el tráfico HTTP no está explícitamente denegado. Esto abre la puerta a ataques de phishing, MiTM (interceptar comunicaciones)...

### WEB-3: Falta la cabecera X-Content-Type-Options 
Es recomendable y buena práctica incluir la cabecera citada, ya que protege a la web de ataques MIME-sniffing.

### WEB-4: Falta de rate-limiting
Se confirma la falta de rate limiting y otras normas similares para proteger los endpoint y los recursos del servidor de ataques de reconocimiento activo agresivos. Usando variedad de herramientas con agresividad alta se detecta la falta de herramientas que impidan a un adversario enumerar recursos del servidor, o incluso con la potencia de cómputo suficiente hacer denegaciones de servicio.
Se verifica elaborando un script que lanza miles de paquetes en cuestión de minutos al servidor, el cual responde cada una de las veces, sin timeouts ni baneos de IP. Además, el endpoint que procesa la información del formulario tampoco está protegido, esto, sumado a la falta de verificación de input, habilita ataques que podrían llenar de información "basura" la base de datos, malgastando espacio y evitando la posibilidad de registro de potenciales clientes.

### IP real
La página se halla tras un load balancer de railway (el proveedor de hosting), de manera que escanear la IP inicial en busca de vulnerabilidades sería inútil. Sin embargo, gracias al hash MD5 del icono de la web, se consigue encontrar la IP original donde el servidor se aloja. El cloud provider es railway, y si ya era un problema la cantidad de información que filtra el servidor en una primera solicitud sencilla, al analizarla accediendo directamente a la IP original es peor

```
FourOhFourRequest: 
    HTTP/1.1 404 Not Found
    Connection: close
    Content-Length: 101
    Content-Type: application/json
    Server: railway-edge
    X-Railway-Edge: railway/europe-west4-drams3a
    X-Railway-Fallback: true
    X-Railway-Request-Id: SDh5vrMESIqe-qsc0-QtfA
    Accept-Ranges: bytes
    Date: Wed, 11 Mar 2026 09:14:36 GMT
    Via: 1.1 varnish
    X-Cache: MISS
    X-Cache-Hits: 0
    X-Timer: S1773220477.962296,VS0,VE36
    Vary: Accept-Encoding
    X-Railway-CDN-Edge: fastly/cache-mad2200108-MAD
    {"status":"error","code":404,"message":"Application not found","request_id":"SDh5vrMESIqe-qsc0-QtfA"}
  GetRequest: 
    HTTP/1.1 404 Not Found
    Connection: close
    Content-Length: 101
    Content-Type: application/json
    Server: railway-edge
    X-Railway-Edge: railway/europe-west4-drams3a
    X-Railway-Fallback: true
    X-Railway-Request-Id: RpB4B8ezSump4qXyw9P4nw
    Accept-Ranges: bytes
    Date: Wed, 11 Mar 2026 09:14:36 GMT
    Via: 1.1 varnish
    X-Cache: MISS
    X-Cache-Hits: 0
    X-Timer: S1773220477.721458,VS0,VE33
    Vary: Accept-Encoding
    X-Railway-CDN-Edge: fastly/cache-mad2200114-MAD
    {"status":"error","code":404,"message":"Application not found","request_id":"RpB4B8ezSump4qXyw9P4nw"}
  HTTPOptions: 
    HTTP/1.1 404 Not Found
    Connection: close
    Content-Length: 101
    Content-Type: application/json
    Server: railway-edge
    X-Railway-Edge: railway/europe-west4-drams3a
    X-Railway-Fallback: true
    X-Railway-Request-Id: 4yXPznYrTviXmcJFALIuDA
    Accept-Ranges: bytes
    Date: Wed, 11 Mar 2026 09:14:36 GMT
    Via: 1.1 varnish
    X-Cache: MISS
    X-Cache-Hits: 0
    X-Timer: S1773220477.844841,VS0,VE32
    Vary: Accept-Encoding
    X-Railway-CDN-Edge: fastly/cache-mad22077-MAD
    {"status":"error","code":404,"message":"Application not found","request_id":"4yXPznYrTviXmcJFALIuDA"}
```



<div class="page-break" style="page-break-before: always;"></div>


## Información de fuentes abiertas
Durante la labor de reconocimiento, se encuentra una cuenta de GitHub con un repositorio de nombre 'ayudapyme'. Se desconoce si GitHub es la tecnología actualmente utilizada por el objetivo, sin embargo, se encuentran varios hilos de los que tirar aunque el código haya cambiado.
https://github.com/ayudapyme/ayudapyme
### RE-1: Repositorio público (CWE-200)
El propio hecho de tener el repositorio público aumenta el riesgo considerablemente.
### RE-2: Endpoint sensible expuesto (CWE-200)
En el código fuente de GitHub se expone un endpoint que contiene información valiosa sobre el funcionamiento interno de la aplicación, expandiendo la superficie de ataque para cualquier adversario que sepa técnicas de OSINT.
```js
//ayudapyme/frontend/src/components/MultiStepForm.tsx:208

const webhookUrl = "https://ayudapyme.app.n8n.cloud/webhook/7c1626a4-96df-4d4b-ad2c-675afd64b257";
```
El endpoint, como se comprueba más adelante, sigue en uso, la instancia de n8n como tal queda fuera de scope, ya que es un servicio que AyudaPyme contrata de una empresa ajena a las pruebas, sin embargo, resulta sencillo descubrir que es crítico para la lógica interna y exponerlo así no es seguro.
### Confirmación de vulnerabilidad WEB-4
Este código permite el envío de solicitudes ilimitadas, permitiendo (potencialmente) ataques de DoS, alta consumición de recursos de cómputo y ataques de fuerza bruta.
```js
//ayudapyme/backend/server.js:13

app.use(cors());
```


<div class="page-break" style="page-break-before: always;"></div>


## Formulario de registro
### MS-1: Falta de validación de input (CWE-20)
![[form.png]]

Se envía el siguiente formulario de registro, con esta prueba se consiguen 3 cosas, en primer lugar, se confirma que no hay ninguna validación de la información que los usuarios introducen en dicho formulario, pudiendo poner teléfonos imposibles, direcciones, CPs y ciudades que no existen...

Probando en más profundidad el endpoint, vemos que no hay ningún tipo de control sobre cuántas request se pueden enviar, ni sobre los campos que el servidor acepta, se podría hacer una herramienta que envíe peticiones por miles al endpoint y llenar la base de datos de basura. También abre las puertas a otras explotaciones mediante inyección, pero ninguna se llega a demostrar

```zsh
curl -X POST https://ayudapyme.app.n8n.cloud/webhook/7c1626a4-96df-4d4b-ad2c-675afd64b257 \  
-H "Content-Type: application/json" \  
-d '{  
 "tamano_empresa": "Autónomo",  
 "actividad": "Ciberseguridad",  
 "cif_nif": "25204046W",  
 "email_facturacion": "audit@test.com",  
 "nombre_titular": "Test User",  
 "domicilio_fiscal": "Calle Falsa 123",  
 "codigo_postal": "28001",  
 "ciudad": "Madrid",  
 "telefono": "600000000",  
 "acepta_terminos": true,  
 "origen": "entrevista-web",  
 "campo_extra": "test"   <-- NO HAY VALIDACIÓN DE CAMPOS
}'  
{"message":"Workflow was started"}
```

### MS-2:  Exposición de información sensible (CWE-200)
![[form-req.png]]

En segundo lugar, de nuevo, el servidor expone en su respuesta un endpoint crítico, que revela mucho sobre la lógica interna y expande la superficie de ataque a cualquiera que revise las respuestas del server. Además, analizando la petición posterior a la primera respuesta del endpoint de n8n, vemos que las credenciales viajan en texto plano (a pesar de ir sobre TLS esto podría ser un problema)

### MS-3: PII en texto plano (CWE-5)
![[req-2.png]]


<div class="page-break" style="page-break-before: always;"></div>

# Mitigación / prevención

## WEB
### WEB-1, WEB-2, WEB-3, WEB-4
Es ideal ocultar toda la información que no sea estrictamente necesaria para un uso legítimo de la aplicación. Esto incluye headers que informan sobre la región, el cloud provider...
Tras investigar, se da [con un post](https://station.railway.com/questions/railway-overwrites-server-header-43f7a398) en el que en principio, según desarrolladores de railway, no es posible evitar esta falla de seguridad en su servicio, sin embargo, al tratarse de un post viejo, es recomendable consultar la documentación o contactar a soporte para confirmarlo.
En cuanto a las otras debilidades, se recomienda añadir las cabeceras [Strict-Transport-Security](https://developer.mozilla.org/es/docs/Web/HTTP/Reference/Headers/Strict-Transport-Security) y [X-Content-Type-Options](https://developer.mozilla.org/es/docs/Web/HTTP/Reference/Headers/X-Content-Type-Options), así como añadir medidas de [rate limiting (limitar peticiones en una ventana de tiempo X)](https://blog.elhacker.net/2025/01/que-es-el-rate-limit-limitar-peticiones-https.html).

## RE
### RE-1, RE-2
Para este punto, sencillamente se recomienda poner el repositorio en privado o directamente eliminarlo si está en desuso, tenerlo público supone exponer lógica e infraestructura.

## MS
### MS-1
Además de las medidas de protección que deberían aplicarse para mitigar la vulnerabilidad WEB-4, que también supondrán una protección contra los ataques que aparecen de esta vulnerabilidad, deberían realizarse algunas comprobaciones adicionales en la información que pasan del formulario a la instancia de n8n. Estas comprobaciones, que ahora se hacen en el frontend, deben realizarse en el backend. De lo contrario, un atacante podría esquivar esos controles haciendo peticiones directamente al webhook de la instancia de n8n. La validación en el frontend está bien implementada y es correcto mantenerla, pero debe ir acompañada de una validación adecuada en el backend.

Hay que llevar a cabo las siguientes validaciones, como mínimo:
- DNI --> [Este artículo](https://jorgerosa.dev/posts/funcion-para-validar-nif-nie-cif-con-javascript/) explica, con una función ya hecha incluida, como verificar con un poco más de rigor si el formato es correcto o no.
- Mail --> Aquí hay [un artículo](https://desarrolloweb.com/articulos/validar-email-javascript) del mismo autor que explica como hacer validaciones de email. Sería ideal además, enviar un correo de confirmación al usuario. Para que nadie pueda llenar la base de datos de correos falsos (ej: fake@fake.fake).

Las validaciones de dirección y teléfono deberían revisarse, especialmente la de dirección, no es difícil implementar algún tipo de validación basada en solicitudes a alguna API de Google o algún organismo público (para verificar que la dirección existe). Por otro lado, el código postal y la ciudad si son realmente sencillos de verificar y ahora mismo no se comprueban.

### MS-2
Ahora mismo, como se aprecia en los diagramas abajo, la información del formulario se envía directamente a n8n para ser procesada ahí. Al ser esta la configuración, el webhook al que va el JSON está expuesto en la petición que provoca el envío del funcionario. Esto permite deducir mucho más de lo que un potencial atacante debería saber sobre la lógica subyacente, y expande significativamente la superficie de ataque que el atacante percibe.
Una forma de corregir esto, por ejemplo, sería tener un backend, al que se envían los datos del formulario. Una vez ahí, los datos se validan y procesa y sólo entonces se mandan a n8n. De esta manera, un atacante solo ve que los datos van al backend, y se revela mucho menos sobre el funcionamiento interno de la app.

![[current_config.jpg|413]] ![[better_config.jpg|444x617]]

### MS-3 
Como bien se explica en [este artículo](https://medium.com/@chathurabimalka/the-importance-of-encrypting-the-entire-payload-even-over-an-https-tls-connection-6999e3d27a53), a pesar de no ser algo de alta urgencia, no es mala práctica cifrar los datos de los clientes, aunque estos viajen por TLS. Añade una capa extra de seguridad a la aplicación y a los datos de los clientes. Para ello hay distintos métodos, como el cifrado de extremo a extremo, en la capa de aplicación o en tránsito. 
