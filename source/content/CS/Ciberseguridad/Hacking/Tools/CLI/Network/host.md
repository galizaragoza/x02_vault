Realiza búsquedas DNS: nombres a IPs y viceversa.

| **Parámetro**   | **Función**                              | **Ejemplo**                                 |
| ----------- | ------------------------------------ | --------------------------------------- |
| `-a`        | Consulta todos tipos.                | `host -a example.com`                   |
| `-c class`  | Especifica clase.                    | `host -c HS _services._dns example.com` |
| `-d` o `-v` | Salida verbose.                      | `host -v example.com`                   |
| `-l`        | Modo lista (zone transfer).          | `host -l example.com`                   |
| `-N ndots`  | Número de dots para nombre absoluto. | `host -N 2 subdomain.example.com`       |
| `-R number` | Número de reintentos UDP.            | `host -R 3 example.com`                 |
| `-r`        | Consultas no recursivas.             | `host -r example.com`                   |
| `-s`        | No consulta si SERVFAIL.             | `host -s example.com`                   |
| `-t type`   | Tipo de consulta.                    | `host -t MX example.com`                |
| `-T`        | Usa TCP.                             | `host -T example.com`                   |
| `-4`        | Fuerza IPv4.                         | `host -4 example.com`                   |
| `-6`        | Fuerza IPv6.                         | `host -6 example.com`                   |
| `-W wait`   | Timeout en segundos.                 | `host -W 5 example.com`                 |
| `-w`        | Espera indefinida.                   | `host -w example.com`                   |
