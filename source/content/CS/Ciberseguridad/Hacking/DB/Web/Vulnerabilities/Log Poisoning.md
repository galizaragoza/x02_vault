### ¿Qué es?
El Log Poisoning es una vulnerabilidad que se acontece cuando los **logs** de un sistema (por ejemplo apache o nginx) **no se sanitizan correctamente**. Esto nos permite ejecutar **código arbitrario** que la máquina víctima interpreta y ejecuta, permitiendo lanzar comandos u obtener una reverse shell.
Un ejemplo común de Log Poisoning sería enviar  `<?php system($_GET['cmd']); ?>` en una petición personalizada con curl o BurpSuite.
### Recursos
[Demostración de El Pingüino de Mario](https://www.youtube.com/watch?v=GQlHNjdD1zc)