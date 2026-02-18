# Autenticación, Autorización y Accounting (Responsabilidad)
A la hora de definir las políticas y medidas de seguridad para nuestra organización, es importante diferenciar entre los conceptos de **autenticación** y **autorización**, así como aplicarlos correctamente en busca de la seguridad mas robusta posible. La 'responsabilidad' consiste en asociar las acciones tomadas por un usuario previamente autenticado y autorizado en un cierto recurso

![[AAA.png|710x398]]
## Autenticación

En términos de seguridad, autenticación se refiere al proceso de demostrar que eres quien dices ser, es decir, si yo soy el usuario Mario, la aplicación tiene que asegurar cuando intento acceder a un **recurso protegido** que efectivamente soy Mario, y tengo los permisos legítimos para consultar esa información.
Un recurso protegido puede ser cualquier cosa que no esté pensada o almacenada para mostrarse públicamente, puede ser ir desde las imágenes del Instagram de alguien publicadas en privado o archivadas, hasta información más crítica como secretos o claves de acceso.

El propósito de la autenticación es garantizar que cuando un agente está solicitando datos o recursos, su identidad coincide con aquella que reclama. Es decir, es quien dice ser. Con la analogía de Instagram, si el usuario @mario tiene una publicación archivada, solo ese usuario debería ser capaz de ver dicha publicación, si el usuario @alejandro pudiese identificarse como @mario y ver la publicación, se trataría de un fallo de autenticación
### Formas de verificar la identidad

1. **Algo que sabes**: Contraseña, passphrase, PIN...
2. **Algo que tienes**: Un badge, llave...
3. **Algo que eres**: Tu iris, huella dactilar, voz...
Es buena práctica combinar dos o incluso más de estas formas, implementando 2FA (Two factor authentication) o MFA
## Autorización
La autorización está muy relacionada pero va por otro lado, es decir, una vez un usuario ya está autenticado, deben estar muy claros y correctamente asignados los **permisos** que ese usuario tiene dentro de los dominios de la aplicación en cuestión. Esto significa que cada usuario, independientemente de su rol, debe tener una seria de normas asignadas a su cuenta que le den permiso o no a tomar acciones, ya sea consultar información, hacer cambios, solicitudes...
En el caso de Instagram, si el usuario @alejandro, autenticado como tal, pudiese desde su cuenta acceder a la publicación archivada de @mario, se trataría de un fallo de autorización, ya que las identidades (ergo autenticación) están bien definidas, pero los permisos de Alejandro son o bien excesivos o mal configurados.
Los ejecutivos y CISOs han de tener muy claro que tipos de cuentas necesitan, que permisos deben tener dichas cuentas y porque, aplicando **siempre** el principio de mínimo privilegio, que consiste en solo autorizar a los usuarios a acciones que sean imprescindibles para desempeñar las tareas que se les asignan

![[min-privilege.png|774x333]]

Este diagrama de la política de permisos de AWS es muy buen ejemplo para entender cuál debería ser la filosofía en términos de políticas de ciberseguridad, negar por defecto, desconfiar por defecto y aplicar mínimo privilegio.
Como ejecutivos, programadores y líderes siempre hemos de preguntarnos: ¿Que es estrictamente necesario permitir a este usuario, servicio, empleado o funcionalidad para que desempeñe su función correctamente? Cualquier cosa que quede fuera de la respuesta no debería permitirse en un primer lugar.

![[AWS_IAM.png]]

## Accounting
Accountability traces an action back to a person or process making this change to a system. Accountability then collects this information and reports the usage data. The organization can use this data for such purposes as auditing or billing. The collected data might include the log-in time for a user, whether the user login was a success or failure, or what network resources the user accessed. This allows an organization to trace actions, errors and mistakes during an audit or investigation.