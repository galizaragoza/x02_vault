
# Parámetros

| **Parámetro**           | **Función**                               | **Sintaxis de Ejemplo** |
| ------------------- | ------------------------------------- | ------------------- |
| `-t`, `--tcp`       | Mostrar sockets TCP                   | `ss -t`             |
| `-u`, `--udp`       | Mostrar sockets UDP                   | `ss -u`             |
| `-w`, `--raw`       | Mostrar sockets RAW                   | `ss -w`             |
| `-x`, `--unix`      | Mostrar sockets UNIX                  | `ss -x`             |
| `-l`, `--listening` | Mostrar solo sockets en escucha       | `ss -l`             |
| `-a`, `--all`       | Mostrar todos los sockets             | `ss -a`             |
| `-n`, `--numeric`   | No resolver nombres de servicio       | `ss -n`             |
| `-p`, `--processes` | Mostrar proceso dueño del socket      | `ss -p`             |
| `-e`, `--extended`  | Mostrar información extendida         | `ss -e`             |
| `-m`, `--memory`    | Mostrar uso de memoria del socket     | `ss -m`             |
| `-i`, `--info`      | Mostrar información interna TCP       | `ss -i`             |
| `-o`, `--options`   | Mostrar información de temporizadores | `ss -o`             |
| `-s`, `--summary`   | Mostrar estadísticas de sockets       | `ss -s`             |
| `-4`, `--ipv4`      | Mostrar solo sockets IPv4             | `ss -4`             |
| `-6`, `--ipv6`      | Mostrar solo sockets IPv6             | `ss -6`             |
| `-0`, `--packet`    | Mostrar sockets PACKET                | `ss -0`             |
| `-H`, `--no-header` | Suprimir línea de encabezado          | `ss -H -t`          |

## Opciones de salida
| **Parámetro**      | **Función**                              | **Sintaxis de Ejemplo**    |
| ------------------ | ---------------------------------------- | -------------------------- |
| `-r`, `--resolve`  | Resolver direcciones y puertos           | `ss -r -t`                 |
| `-N`, `--net`      | Espacio de nombres de red                | `ss -N /var/run/netns/ns1` |
| `-b`, `--bpf`      | Filtrar con BPF (Berkeley Packet Filter) | `ss -b`                    |
| `-E`, `--events`   | Modo continuo de eventos                 | `ss -E -t`                 |
| `-Z`, `--context`  | Mostrar contexto de seguridad SELinux    | `ss -Z`                    |
| `-z`, `--contexts` | Mostrar contexto y proceso               | `ss -z`                    |
| `--tos`            | Mostrar tipo de servicio (TOS)           | `ss --tos`                 |
| `--cgroup`         | Mostrar cgroup del socket                | `ss --cgroup`              |
| `--csv`            | Salida en formato CSV                    | `ss --csv -t`              |
## Filtros avanzados
| **Expresión**           | **Función**                       | **Sintaxis de Ejemplo**       |
| ------------------- | ----------------------------- | ------------------------- |
| `state ESTABLISHED` | Sockets en estado ESTABLISHED | `ss -t state ESTABLISHED` |
| `state LISTENING`   | Sockets en estado LISTENING   | `ss -t state LISTENING`   |
| `state CLOSED`      | Sockets en estado CLOSED      | `ss -t state CLOSED`      |
| `state SYN-SENT`    | Sockets en estado SYN-SENT    | `ss -t state SYN-SENT`    |
| `state SYN-RECV`    | Sockets en estado SYN-RECV    | `ss -t state SYN-RECV`    |
| `state FIN-WAIT-1`  | Sockets en estado FIN-WAIT-1  | `ss -t state FIN-WAIT-1`  |
| `state FIN-WAIT-2`  | Sockets en estado FIN-WAIT-2  | `ss -t state FIN-WAIT-2`  |
| `state TIME-WAIT`   | Sockets en estado TIME-WAIT   | `ss -t state TIME-WAIT`   |
| `state CLOSING`     | Sockets en estado CLOSING     | `ss -t state CLOSING`     |
| `state LAST-ACK`    | Sockets en estado LAST-ACK    | `ss -t state LAST-ACK`    |
| `state ALL`         | Todos los estados             | `ss -t state ALL`         |
## Estados TCP
| **Estado**        | **Descripción**                          | **Comando para monitorear**   |
| ------------- | ------------------------------------ | ------------------------- |
| `LISTEN`      | Escuchando conexiones entrantes      | `ss -t state LISTEN`      |
| `ESTABLISHED` | Conexión establecida                 | `ss -t state ESTABLISHED` |
| `SYN-SENT`    | Cliente envió SYN                    | `ss -t state SYN-SENT`    |
| `SYN-RECV`    | Servidor recibió SYN                 | `ss -t state SYN-RECV`    |
| `FIN-WAIT-1`  | Primer FIN enviado                   | `ss -t state FIN-WAIT-1`  |
| `FIN-WAIT-2`  | ACK recibido del primer FIN          | `ss -t state FIN-WAIT-2`  |
| `TIME-WAIT`   | Esperando que paquetes desaparezcan  | `ss -t state TIME-WAIT`   |
| `CLOSED`      | Conexión cerrada                     | `ss -t state CLOSED`      |
| `CLOSE-WAIT`  | Esperando que aplicación cierre      | `ss -t state CLOSE-WAIT`  |
| `LAST-ACK`    | Esperando ACK del FIN propio         | `ss -t state LAST-ACK`    |
| `CLOSING`     | Ambos lados cerraron simultáneamente | `ss -t state CLOSING`     |