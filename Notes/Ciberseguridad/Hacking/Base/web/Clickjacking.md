### ¿Qué es el clickjacking?
El clickjacking acontece cuando un atacante camufla, mediante código en la UI, acciones, en forma de formularios, botones... "Robando" o hijacking así los clicks del usuario, de ahí el nombre.
Por ejemplo, la noticia citada más abajo narra como los 11 principales password managers eran total o parcialmente vulnerables a al clickjacking a través del siguiente vector de ataque: Reduciendo la opacidad a cero de ciertos elementos de la interfaz y jugando con ocultar ciertas partes de la web a la vista, un formulario invisible se rellenaba con las credenciales del password manager gracias a la función de autocompletado de estos. El cliente presiona en un botón de cebo aparentemente inofensivo con algo como "Gana un iPhone!", el usuario, con la guardia baja debido a la opacidad cero del formulario, clica en el botón, enviando sus credenciales a un servidor remoto propiedad del atacante. 
### Recursos
### Artículos
[Artículo de Owasp](https://owasp.org/www-community/attacks/Clickjacking)
[Noticia sobre vulnerabilidad clickjacking en password managers](https://thehackernews.com/2025/08/dom-based-extension-clickjacking.html)
[Attack example Imperva](https://www.imperva.com/learn/application-security/clickjacking/)