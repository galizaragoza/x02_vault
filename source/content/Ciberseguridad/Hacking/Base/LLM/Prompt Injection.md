### ¿Qué es Prompt Injection?
El prompt injection es una técnica mediante la cual se pretende atacar a modelos de inteligencia artificial vulnerables, buscando burlar medidas de seguridad que el modelo en cuestión pueda tener usando ciertas frases clave o incluso código.
En resumen, se trata de obtener una respuesta manipulada, ya sea con contenido dañino o bloqueado mediante inputs específicos que alteran el comportamiento del modelo. Por ejemplo, en este artículo de [The Hackers News](https://thehackernews.com/2025/08/someone-created-first-ai-powered.html) podemos leer al final del mismo la siguiente cita:
	*Adding phrases like 'use compatibility mode' or 'fast response needed' bypasses millions of dollars in AI safety research*
En el ejemplo concreto de la noticia, los atacantes, con la intención de llevar a cabo ataques de ransomware, fueron capaces de generar scripts en tiempo real con IA utilizando esta misma técnica y burlando los controles de seguridad de OpenAI apelando a un modelo anterior no securizado.

Fijándonos en esta otra noticia de [Help Net Security](https://www.helpnetsecurity.com/2025/09/05/ai-agents-prompt-injection-poisoned-web/) vemos como la subida en la creación de páginas web repletas de contenido malicioso dirigido específicamente a IAs es tan grande que se ha llegado a acuñar el término *“parallel-poisoned web”*, un formato de ataque a gran escala y largo plazo que resulte muy díficil de detectar, si combinamos esto con el conocimiento que tenemos de lo agresivas que son las campañas de scraping de las empresas líder del sector de la IA podemos imaginar la gravedad del asunto.
[Anthropic tumba página después de hacer casi 1 millón de solicitudes en menos de 34h](https://x.com/kwiens/status/1816128302542905620)
[ChatGPT da instrucciones para "desenchufar una naranja" y "desmontar un plátano" por un falso artículo publicado a propósito para demostrar el scraping](https://chatgpt.com/share/e52dc4dd-77e6-48a5-a7ca-77e3dfa39eb5)

Hay esencialmente dos tipos de ataques de prompt injection, los **directos** y los **indirectos**: 
Los **ataques directos** acontecen cuando el prompt del usuario altera directamente el comportamiento del modelo, ya sea de forma intencional o no.
Por otro lado, los **ataques indirectos** se dan cuando el modelo acepta inputs de terceros o fuentes externas, ya sean sitios web, imágenes... El atacante podría haber ocultado o camuflado instrucciones maliciosas que cambien el comportamiento del modelo de forma inesperada. Por ejemplo, un atacante podría dar un prompt aparentemente inofensivo en texto plano, pero adjuntar una imagen con instrucciones maliciosas a este texto, manipulando el modelo para burlar las medidas de seguridad.
Los ataques de prompt injection suponen una amenaza cada vez mayor conforme vamos dando más y más autoridad y control sobre las infraestructuras a los modelos de lenguaje, y algo tan común a y aparentemente inofensivo como un chatbot de atención al cliente puede dar pie a vulnerablidades gravísimas como:
- Exfiltración de información sensible de la empresa usuaria(secretos industriales, datos personales...)
- Exfiltración de información sensible sobre el propio modelo.
- Manipulación del contenido, resultando en en respuestas sesgadas o incorrectas
- Acceso no autorizado a funciones disponibles al LLM
- Ejecución de código/comandos arbitrarios en sistemas conectados
![[prompt-injection.png]]

### Recursos
### Artículos
[Artículo de la OWASP](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
[Artículo de Palo Alto Networks](https://www.paloaltonetworks.com/cyberpedia/what-is-a-prompt-injection-attack)
[Guía paso a paso explotando un chatbot hasta obtener RCE](https://www.netspi.com/blog/technical-blog/ai-ml-pentesting/how-to-exploit-a-generative-ai-chatbot-using-prompt-injection/)
[Impresionante demostración de un caso real de ataque prompt injection](https://www.youtube.com/watch?v=JNHpZUpeOCg) --> Se inyecta a través de un archivo malicioso compartido con la víctima vía Google Drive (es necesario que la víctima tenga Drive conectado a ChatGPT) instrucciones ocultas en el documento que exfiltran la totalidad del Drive 
[Stealthy attack serves poisoned web pages only to AI agents](https://www.helpnetsecurity.com/2025/09/05/ai-agents-prompt-injection-poisoned-web/)
[# Ticket2Secret: When a Jira Ticket Can Steal Your Secrets](https://youtu.be/l9gTcfUJOcc?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
