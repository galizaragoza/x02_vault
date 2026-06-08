**WAFW00F** es una herramienta de Python diseñada para identificar y huellar (fingerprinting) Sistemas de Prevención de Intrusiones (IPS) y Cortafuegos de Aplicaciones Web (**WAF**) que protegen un sitio web.

| **Parámetro**     | **Función**                                                      | **Ejemplo de Sintaxis**                              |
| ----------------- | ---------------------------------------------------------------- | ---------------------------------------------------- |
| `-l`, `--list`    | Lista todos los WAFs que la herramienta puede detectar.          | `wafw00f -l`                                         |
| `-p`, `--proxy`   | Utiliza un proxy para realizar las peticiones.                   | `wafw00f http://target.com -p http://127.0.0.1:8080` |
| `-a`, `--findall` | No se detiene al hallar el primer WAF; busca todos los posibles. | `wafw00f http://target.com -a`                       |
| `-i`, `--input`   | Lee una lista de dominios desde un archivo de texto.             | `wafw00f -i targets.txt`                             |
| `-v`, `--verbose` | Aumenta el nivel de detalle de la salida (puedes usar `-vv`).    | `wafw00f http://target.com -v`                       |
| `-H`, `--headers` | Permite añadir cabeceras personalizadas a las peticiones.        | `wafw00f -H "User-Agent: Mozilla/5.0"`               |