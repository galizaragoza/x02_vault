
| **Parámetro/Opción** | **Función**                                                 | **Ejemplo de Sintaxis**              |
| ---------------- | ------------------------------------------------------- | -------------------------------- |
| `@servidor`      | Especifica servidor DNS a consultar                     | `dig @8.8.8.8 ejemplo.com`       |
| `-t tipo`        | Tipo de registro DNS a consultar (A, MX, NS, TXT, etc.) | `dig -t MX ejemplo.com`          |
| `-x`             | Consulta DNS inversa (PTR)                              | `dig -x 8.8.8.8`                 |
| `+short`         | Muestra solo la respuesta breve                         | `dig ejemplo.com +short`         |
| `+trace`         | Traza la resolución DNS completa                        | `dig ejemplo.com +trace`         |
| `+noall +answer` | Solo muestra la sección de respuesta                    | `dig ejemplo.com +noall +answer` |
| `+stats`         | Muestra estadísticas de la consulta                     | `dig ejemplo.com +stats`         |
| `-f archivo`     | Lee consultas desde archivo                             | `dig -f consultas.txt`           |
| `-4` / `-6`      | Usa solo IPv4 o IPv6                                    | `dig -4 ejemplo.com`             |
| `+time=T`        | Tiempo máximo de espera (segundos)                      | `dig ejemplo.com +time=5`        |
| `+retry=N`       | Número de reintentos                                    | `dig ejemplo.com +retry=3`       |
