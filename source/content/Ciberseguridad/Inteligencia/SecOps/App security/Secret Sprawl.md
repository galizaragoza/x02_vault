### ¿Qué es "secrets sprawl"?
En el contexto del desarrollo, los secretos son credenciales de autenticación digitales que permiten acceder a sistemas o información privados. A menudo toman forma de llaves API, nombres de usuario y contraseñas o certificados de seguridad.
En el internet de hoy, los secretos no son una llave maestra, la filosofía de desarrollo y toma de decisiones de la construcción de un servicio o web que hemos adoptado hace que las aplicaciones estén compuestas decenas o incluso miles de bloques independientes con los que construir el producto. Las comunicaciones son por Slack, la página se aloja en AWS, los pagos son a través de Stripe y un largo etcétera.

### La importancia de garantizar la seguridad de los secretos
Los secretos son los puentes que se tienden entre estos distintos bloques y los convierten en una máquina que trabaja como una única unidad para cumplir cualquiera que sea el/los propósito(s) de su desarrollo. Por este motivo, tener políticas muy fuertes para asegurar la protección de estos secretos es fundamental para garantizar la salud de la empresa o el proyecto, ya que la exposición, exfiltración o robo de uno solo puede acarrear daños gravísimos, o, peor aún, desencadenar una reacción en cadena que acabe comprometiendo la totalidad de la infraestructura.

### Dónde se encuentran estos secretos y cómo se ven comprometidos
A diferencia de las credenciales convencionales, como la contraseña de cada uno para su cuenta de una red social, estos secretos existen para ser compartidos dentro del ecosistema del equipo de desarrollo, para que los trabajadores puedan desempeñar su función y coordinar todos estos sistemas en una aplicación funcional. 
### Cómo afrontar esta amenaza

### Fuentes
- https://www.gitguardian.com/glossary/secret-sprawl-definition
- https://www.gitguardian.com/files/the-state-of-secrets-sprawl-report-2025
- https://www.helpnetsecurity.com/2025/09/02/gitguardian-secrets-sprawl-video/
