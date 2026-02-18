# **Trabajo final CiberInteligencia**
# Por Mario Hinojosa Freire
---

# Índice
- 
	- Metodología y contexto
	- ¿En que consiste el malware?
		- Diagrama
		- Revisión del código y demostración
			- Vídeo
			- Repositorio
- 

<div class="page-break" style="page-break-before: always;"></div>

# Metodología y contexto
En base al enunciado de este ejercicio, se ha elaborado una pieza de software maliciosa pensada para explotar equipos Ubuntu con versiones del kernel para las cuales haya disponibles exploits públicos.

En el proyecto se asume un escenario en el cual ya ha habido una intrusión previa, es decir, como atacante ya hay acceso a la máquina objetivo. Partiendo de esa base hipotética, el software desarrollado reconoce información crítica del sistema, la procesa y genera un exploit con el que escalar privilegios en la máquina víctima, con ellos se establece un canal persistente y silencioso y se eliminan las pruebas del ataque.

El software ha sido desarrollado en Python íntegramente por mí consultando documentación. Consiste de dos archivos, uno en la máquina víctima y otro en la atacante, que se comunican vía HTTP, pero eso se explica en profundidad más adelante.

En este informe hay 2 secciones principales, la primera consiste en una explicación verbal, con fragmentos de código y diagramas de lo que hace a grandes rasgos el software, cual es su propósito, la lógica que lo hace funcionar y como sus partes interactúan. En segundo lugar, se lleva a cabo una demostración intercalando imágenes, vídeos y fragmentos del código de cómo funciona en un objetivo lugar la explotación, la persistencia y la limpieza de pruebas.

<div class="page-break" style="page-break-before: always;"></div>

# ¿En que consiste el malware?

> IMPORTANTE: Como se menciona arriba, en este proyecto se asume una intrusión previa a la máquina objetivo, sólo está testeado en Ubuntu 16.04 (aunque debería funcionar sin problemas en cualquier versión con kernel vulnerable de Ubuntu y otras distros, siempre el script `target.py` envíe a la máquina atacante una versión de kernel vulnerable y la máquina disponga de Python 3.8+).

A grandes rasgos, el código hace lo siguiente: El malware está formado por dos archivos, uno de ellos se ejecuta en la máquina atacante (kali) y el otro se ejecuta en la máquina víctima, en este caso, se trata de una máquina virtual Ubuntu Server 16.04.4.
El ejecutar el archivo `target.py`, en la víctima, este extrae información sobre la versión del kernel de la máquina objetivo, y la envía a la máquina atacante `attacker.py`. Esto es posible porque el código de la máquina atacante es un servidor HTTP, la información extraída por `target` es enviada en una solicitud HTTP al servidor de `attacker`, una vez ahí, se procesa, y se comprueba si la versión recibida de la víctima es vulnerable. Entonces se comprueba si existen exploits públicos, y en caso de haberlos, se descarga el código, se compila en un contenedor (para compatibilidad con las librerías) y se envía a la víctima, donde se ejecuta.

<div class="page-break" style="page-break-before: always;"></div>

## Diagrama
Como se puede observar en el diagrama(1) y como se ha explicado arriba, la máquina víctima envía por medio del script la información del kernel a la máquina atacante, cuyo respectivo script actúa como servidor HTTP. Esa información se procesa, y sirviéndose de Docker para solventar posibles problemas de compatibilidad de librerías de C, compila un exploit para dicha versión.

Una vez ya hay un binario en la máquina atacante, y gracias a un loop que hay en el script `target.py`, se descarga el exploit en la máquina víctima, si la versión del kernel es en efecto vulnerable, el exploit se ejecuta, ganando privilegios *root*.
Cuando dichos privilegios se han conseguido, se borra el rastro de la actividad que acaba de acontecer en el equipo y se establece un canal persistente entre la máquina atacante y la víctima, que permite al atacante ganar acceso de nuevo en cualquier momento.

![[general_diagram.jpg|475x514]]     ![[funciones_endpoints_diag.jpg|486x514]]

A nivel de funciones, como se ve en el diagrama(2), sucede lo siguiente.
1. `get_target_info()` envía la información recopilada sobre el kernel al endpoint `/target_info`
2. `/target_info` invoca `compile_exploit()`, que procesa la información y compila un binario para explotar el objetivo
3. Durante los pasos 1 y 2, `wait_and_exploit()` está esperando a que el/los binarios estén listos, una vez están disponibles, los descarga de un endpoint dinámico cuyo nombre es el mismo que el del binario y los ejecuta
4. Pasa los privilegios conseguidos por el exploit a `persistence()` y `clean_and_close()`, las cuáles abren un backdoor en la víctima y limpian el rastro de la actividad, respectivamente.

<div class="page-break" style="page-break-before: always;"></div>

## Revisión del código y demostración
Para la revisión del código y demostración del exploit, se ha optado por explicarlo en formato vídeo, el cual está disponible en el siguiente enlace.
### [Vídeo: explicación y demo](https://youtu.be/Irg9c34oHXQ)

Si se quiere también disponer del código fuente para examinarlo más detenidamente, está disponible en este repositorio.
### [Repositorio GitHub](https://github.com/galizaragoza/Ubuntu_16.04_auto_exploit.git)