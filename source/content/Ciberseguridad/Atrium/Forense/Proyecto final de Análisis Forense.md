## Antes de analizar las evidencias
### Análisis de la amenaza en la sede Australiana
Sin lugar a dudas, el ataque sufrido en la sede Australiana es un ataque de phishing masivo. El phishing es un tipo de ciberataque basado en la ingeniería social que explota "vulnerabilidades humanas", como la avaricia, miedo, o en este caso falta de concienciación.

Los atacantes han aprovechando la falta de formación de los empleados para conducirlos, a partir de un email fraudulento, a una página de login que imita a la de un servicio legítimo de la empresa (Office  365), pero que envía sus credenciales a un servidor controlado por el atacante en lugar de autenticarlos en la plataforma real.
Posteriormente, el atacante se aprovecha de las débiles políticas de autenticación de la compañía (falta de autenticación MFA), para ganar acceso a las cuentas de los empleados que han caído previamente en el engaño. Una vez los atacantes tienen acceso a la infraestructura de la organización con las autorizaciones de aquellos empleados víctimas del ataque, los atacantes pueden acceder a todos los activos de la organización a los cuales dichos empleados tienen acceso legítimo, y proceder a su exfiltración, ya sea con fines de daño reputacional, económico o estratégico.

#### Análisis de los riesgos potenciales en base al incidente de Australia
Tras la brecha inicial en el ataque de phishing acontecido en la sede de Australia, hay varias vías mediante las cuales el atacante podría empeorar la situación o prolongar su intrusión en la infraestructura.
1. En primer lugar, si no se lidia con la amenaza de forma eficaz y rápida, la exfiltración podría continuar, resultando en mayor daño reputacional, económico o estratégico.
2. Además, dependiendo de la naturaleza de la exfiltración, la empresa podría estar cometiendo delitos de incumplimiento de protección de datos según la legislación vigente, resultando en aún más daños reputacionales y multas.
3. Además, si al atacante (aún infiltrado) logra dar con un vector de escalada y consigue elevar sus privilegios, podría llegar a cometer otros delitos como el caso de ransomware visto en España.
4. Si la respuesta al incidente no es suficiente, el atacante podría mantener su presencia en la organización y volver al ataque el en futuro
<div class="page-break" style="page-break-before: always;"></div>

### Análisis del ciberincidente en la sede de Madrid
El evento acontecido en la infraestructura de la sede Española es un caso de ransomware. Aprovechándose de un acceso inicial que está aún por diagnosticar, un atacante o grupo de atacantes han ganado acceso a los sistemas de la compañía. Una vez conseguido el acceso privilegiado, ya sea mediante escalada o una política de autorización débil, los atacantes han logrado conseguir permisos de administrador y han encriptado todos los archivos, haciéndolos totalmente irrecuperables sin la llave adecuada.
El objetivo del ataque es mantener toda la información valiosa contenida en esos archivos como "rehén", y ahora el atacante solicita un rescate a la compañía a cambio de la llave con la que recuperar esos archivos.
<div class="page-break" style="page-break-before: always;"></div>

## Sobre el incidente en Italia
Dependiendo de lo crítico que sea mantener las operaciones para las cuáles se requieren los sistemas afectados, puede no merecer la pena su desconexión. La decisión depende del plan de continuidad de negocio pertinente, en cualquier caso, lo recomendable sería desconectar el equipo para evitar que el atacante pueda pivotar por la red y expandir su alcance.
En caso de que tras evaluar el plan de continuidad de negocio, se decida desconectar el equipo, es necesario clonar sus unidades de almacenamiento, tanto permanente como volátil, así como las del servidor de logs, ya que pueden contener información valiosa sobre el incidente y si el atacante ya ha logrado pivotar la integridad de estos registros corre peligro.
<div class="page-break" style="page-break-before: always;"></div>

## Diagnóstico incidente Australia
Se ha desarrollado un script en `python` que parsea el logfile del tráfico de red y lo muestra por salida de CLI, está adjunto en la entrega.

### Correos afectados
Analizando el tráfico de la página, eventualmente se da con el dominio malicioso.

```
suspicious.microsoft.logon.fadel.id
```

Asociado a la IP

```
49.50.8.230
```

![[malicious-url.png]]

A partir de la información contenida en el paquete encontrado, podemos deducir que la query que se está enviando mediante `GET` al servidor del atacante está encodeada en base64, al desencodearla, nos encontramos con lo siguiente:

```
mgarcia@invent.com:manzana123
https://pastebin.com/2R0Fem3C
```

De esta información se elabora la siguiente hipótesis: Los empleados víctima del phishing reciben un email que contiene un enlace aparentemente legítimo al portal de ofimática corporativo de Invent S.L.

El panel de login, es en realidad un sitio malicioso controlado por el atacante que envía por `GET` las credenciales del empleado al atacante con el formato `usuario:contraseña`. Además, el enlace contiene un enlace de `pastebin`, una web en la que se pueden compartir de forma anónima bloques de texto, al acceder a ese enlace vemos otros tres correos

```
hifid@invent.com:123dmr

hjerfs@invent.com:applepup

jdarwin@invent.com:redcar#
```

De esta investigación se deduce la siguiente lista de correos afectados:
- mgarcia@invent.com
- hifid@invent.com
- hjerfs@invent.com
- jdarwin@invent.com <div class="page-break" style="page-break-before: always;"></div>

## Diagnóstico incidente Madrid
### ¿Cómo funciona?
Tras una brecha inicial, sea cual sea el vector de ataque/escalada, el atacante consigue los privilegios necesarios para encriptar todos los archivos (o archivos necesarios para el funcionamiento del sistema) de uno o varios equipos. Esto hace que el equipo quede totalmente inservible y su información irrecuperable, entonces, el atacante pide un rescate a cambio de la llave para recuperar información.

### ¿Es posible la recuperación?
En todos los casos de ransomware, siempre existe la opción de pagar el rescate. Evidentemente, no es la mejor opción, ya que sin conocer la reputación del atacante no hay garantías de que se pueda recuperar la información aún después de pagar.

Basándome en la captura de la notificación del ransomware, se ha determinado que se trata de la distribución NM4, la cual puede ser eliminada con relativa sencillez siguiendo unos pasos o con software de terceros.
En lo que respecta a los archivos, lo ideal sería restaurarlos desde una copia de seguridad previamente hecha, de todas formas, en caso de que no haya tal cosa, hay otras opciones:
1. **Software de terceros para recuperación de archivos**
2. **System Restore Point**
3. **File History**

### Diagnóstico del vector de entrada
Un servidor SMB en el puerto 139 ha quedado expuesto a Internet, el atacante probablemente ha recopilado toda la información posible de ese servidor y la ha aprovechado para ganar acceso no autorizado a los sistemas (probablemente mediante credenciales recicladas o alguna otra información similar).
<div class="page-break" style="page-break-before: always;"></div>

## Prevención
### Australia
Algunas medidas que podrían implementarse para evitar que vuelva a suceder algo así:
1. Formación periódica y concienciación de los empleados.
2. Implementación de firewalls que bloqueen dominios e IPs no confiados.
3. Aplicar el principio de cero confianza: Desconfiar de toda acción, solicitud, input o paquete hasta que se verifique su legitimidad o venga de una fuente confiada.
4. Implementación de firewalls host-based en cada equipo, operan en la capa 7 del modelo OSI y aumentan la protección frente a este tipo de ataques.
5. Auditar mediante terceros frecuentemente la organización

### Madrid
Respecto al incidente de Madrid:
1. Aplicar el principio de cero confianza: Desconfiar de toda acción, solicitud, input o paquete hasta que se verifique su legitimidad o venga de una fuente confiada.
2. No exponer a Internet todo servicio que no sea absolutamente necesario.
3. Implementar soluciones IDS (Intrusion Detection System) o IPS (Intrusion Prevention System) para proteger la infraestructura en caso de haber una brecha inicial.
4. Revisar y asegurar que se cumple con las políticas de empresa, el plan de respuesta a incidentes y continuidad de negocio.
5. Revisar y mejorar los sistemas para reforzar el principio AAA (Autenticación, Autorización y Responsabilidad).
6. Hacer copias de seguridad frecuentes y mantener el software parcheado y al día.
7. Auditar mediante terceros frecuentemente la organización.