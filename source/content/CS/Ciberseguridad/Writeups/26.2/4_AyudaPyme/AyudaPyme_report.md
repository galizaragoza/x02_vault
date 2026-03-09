# Introducción
En este documento se explica detalladamente un ejercicio de penetración ofensiva llevado a cabo sobre la start-up AyudaPyme, en este documento se explica tanto en un resumen ejecutivo (no técnico), como en más detalle en la sección técnica todo el proceso de las pruebas, desde el reconocimiento de la infraestructura y la recopilación de información de fuentes abiertas hasta los entresijos descubiertos del funcionamiento de la aplicación

<div class="page-break" style="page-break-before: always;"></div>

# Índice

<div class="page-break" style="page-break-before: always;"></div>

# Metodología

<div class="page-break" style="page-break-before: always;"></div>

# Resumen técnico
Este sección contiene una tabla con un resumen técnico de los hallazgos encontrados durante el ejercicio de las pruebas, la tabla contiene la información siguiente con la distribución
- **Código**: Un identificador interno de este documento para referenciar la vulnerabilidad en cuestión
- **Gravedad**: Una etiqueta evaluando la gravedad del hallazgo (informativo, leve, medio, grave, crítico)
- **Síntesis**: Breve explicación de la vulnerabilidad
- **CWE**: Código del estándar [Common Weakness Enumeration](https://cwe.mitre.org/data/definitions/200.html)
- Enlace a la **demostración** de la vulnerabilidad en el documento
- Enlace a las buenas prácticas y medidas de **mitigación/prevención** en el documento

| Código (ID) | Gravedad | Síntesis                                    | CWE                                                        | Demostración                                                                | Mitigación |
| ----------- | -------- | ------------------------------------------- | ---------------------------------------------------------- | --------------------------------------------------------------------------- | ---------- |
| RE-1        | Leve     | Demasiada info. en cabeceras HTTP           | [CWE-200](https://cwe.mitre.org/data/definitions/200.html) | [[AyudaPyme_report#RE-1\|RE-1]]                                             |            |
| RE-2        | Medio    | Falta la cabecera Strict-Transport-Security | [CWE-523](https://cwe.mitre.org/data/definitions/523.html) | [[AyudaPyme_report#RE-2 Falta la cabecera Strict-Transport-Security\|RE-2]] |            |
| RE-3        | Leve     | Falta la cabecera X-Content-Type-Options    | [CWE-644](https://cwe.mitre.org/data/definitions/644.html) | [[AyudaPyme_report#RE-3\|RE-3]]                                             |            |

<div class="page-break" style="page-break-before: always;"></div>

# Informe
## Información del servidor
Gracias a `dig` se averigua la IP real del servidor y el hosting en que se aloja la página.

```zsh
www.ayudapyme.es.       1800    IN      CNAME   pj53zmgn.up.railway.app.  
pj53zmgn.up.railway.app. 60     IN      A       66.33.22.2
```

Al cabo de unas horas, se vuelve a hacer la misma consulta y la IP cambia, por lo que se da por echo que el proveedor de hosting rota las IPs cada X días. El periodo está por determinar, ya que se hace la consulta de nuevo tras 3 días y la IP es la misma que con la segunda.

```zsh
www.ayudapyme.es.       1800    IN      CNAME   pj53zmgn.up.railway.app.  
pj53zmgn.up.railway.app. 60     IN      A       151.101.66.15
```

---
## Cabeceras HTTP
### RE-1: Demasiada info. en cabeceras HTTP
Analizando las respuestas del servidor, se identifica un exceso de información proporcionada a potenciales adversarios en las cabeceras HTTP, de una simple petición a la raíz del servidor, se puede identificar el proveedor de cloud, CDN. La respuesta se saca, sencillamente, de la herramienta de desarrollador del navegador.

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

Además, en el análisis de esta respuesta del servidor de identificar otras vulnerabilidades

### RE-2: Falta la cabecera Strict-Transport-Security  
La cabecera Strict-Transport-Security no está definida, esto significa que a pesar de que el servidor permite tráfico HTTPS y utiliza TLS, el tráfico HTTP no está explícitamente denegado.

### RE-3: Falta la cabecera X-Content-Type-Options 
Es recomendable y buena práctica incluir la cabecera citada, ya que protege a la web de ataques MIME-sniffing.

---
## OSINT
Durante la labor de reconocimiento, se encuentra una cuenta de GitHub con un repositorio de nombre 'ayudapyme'. Se desconoce si GitHub es la tecnología actualmente utilizada por el objetivo, sin embargo, se encuentran varios hilos de los que tirar aunque el código haya cambiado.
https://github.com/ayudapyme/ayudapyme
### RE-4: Repositorio público (CWE-200)
El propio hecho de tener el repositorio público aumenta el riesgo considerablemente.
### RE-5: Endpoint sensible expuesto (CWE-200)
En el código fuente de GitHub se expone un endpoint que contiene información valiosa sobre el funcionamiento interno de la aplicación, expandiendo la superficie de ataque para cualquier adversario que sepa técnicas de OSINT.
```js
//ayudapyme/frontend/src/components/MultiStepForm.tsx:208

const webhookUrl = "https://ayudapyme.app.n8n.cloud/webhook/7c1626a4-96df-4d4b-ad2c-675afd64b257";
```
### RE-6: Falta de rate-limiting (CWE-770)
Este código permite el envío de solicitudes ilimitadas, permitiendo2 (potencialmente)ataques de DoS, alta consumición de recursos de cómputo y ataques de fuerza bruta.
```js
//ayudapyme/backend/server.js:13

app.use(cors());
```
Esto se confirma usando una herramienta que hace 5000 solicitudes en apenas 5 minutos, y todas y cada una de ellas responden con un 200 (OK) y no establecen ningún tipo de timeout o baneo a la IP de la que han venido estas solicitudes.

----
## n8n 
De nuevo haciendo uso de dig se identifica la IP de n8n. En este caso ambas de las IPs averiguadas están tras un firewall de Cloudflare.
```
;; ANSWER SECTION:  
ayudapyme.app.n8n.cloud. 236    IN      A       172.67.72.185  
ayudapyme.app.n8n.cloud. 236    IN      A       104.26.13.13  
ayudapyme.app.n8n.cloud. 236    IN      A       104.26.12.13
```

![[cloudflare_waf.png]]

Debido a esto, el método a proceder es analizar ambas en profundidad e intentar burlar el WAF de Cloudflare para identificar los recursos expuestos de esa instancia.

---
## Formulario de registro
### MS-1: Falta de validación de input (CWE-20)
![[form.png]]

Se envía el siguiente formulario de registro, con esta prueba se consiguen 3 cosas, en primer lugar, se confirma que no hay ninguna validación de la información que los usuarios introducen en dicho formulario, pudiendo poner teléfonos imposibles, direcciones, CPs y ciudades que no existen...

### MS-2:  Exposición de información sensible (CWE-200)
![[form-req.png]]

En segundo lugar, de nuevo, el servidor expone en su respuesta un endpoint crítico, que revela mucho sobre la lógica interna y expande la superficie de ataque a cualquiera que revise las respuestas del server. Además, analizando la petición posterior a la primera respuesta del endpoint de n8n, vemos que las credenciales viajan en texto plano (a pesar de ir sobre TLS esto podría ser un problema)

### MS-3: PII en texto plano (CWE-5)
![[req-2.png]]

### MS-4: Endpoint vulnerable (CWE-20)
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


<div class="page-break" style="page-break-before: always;"></div>

# Mitigación / prevención

## Medio

