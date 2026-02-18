| **Parámetro** | **Función**                                                     | **Ejemplo de Sintaxis**           |
| ------------- | --------------------------------------------------------------- | --------------------------------- |
| `-t`          | Especifica el tipo de registro a buscar (A, MX, TXT, etc.).     | `host -t mx google.com`           |
| `-a`          | Modo "Any" (equivalente a `-v -t ANY`). Muestra todo.           | `host -a google.com`              |
| `-l`          | Lista todos los hosts en un dominio (transferencia de zona).    | `host -l dominio.com ns1.dns.com` |
| `-v`          | Modo detallado (verbose), muestra la respuesta DNS completa.    | `host -v google.com`              |
| `-r`          | Desactiva la recursividad (solo consulta al servidor indicado). | `host -r google.com`              |
| `-W`          | Define el tiempo de espera (timeout) en segundos.               | `host -W 5 google.com`            |