# ss

**`ss`** (socket statistics) es la utilidad de `iproute2` para inspeccionar sockets: conexiones TCP/UDP, sockets UNIX de dominio, RAW, PACKET y otras familias. Lee el estado directamente del kernel vía netlink (`sock_diag`), por lo que es más rápida y detallada que su predecesora [[netstat]]. Muestra estados de conexión, procesos dueños, temporizadores, memoria y métricas internas TCP, y admite un lenguaje de filtros por estado y por dirección. Sintaxis base: `ss [opciones] [filtro]`.

```
ss [ -aA46nHroempi... ] [ -f FAMILY ] [ FILTER ]
```

---

## Selección de familia / tipo de socket

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-t`, `--tcp` | Sockets TCP. | `ss -t` |
| `-u`, `--udp` | Sockets UDP. | `ss -u` |
| `-w`, `--raw` | Sockets RAW. | `ss -w` |
| `-x`, `--unix` | Sockets UNIX de dominio. | `ss -x` |
| `-0`, `--packet` | Sockets PACKET (capa 2). | `ss -0` |
| `-d`, `--dccp` | Sockets DCCP. | `ss -d` |
| `-S`, `--sctp` | Sockets SCTP. | `ss -S` |
| `-M`, `--mptcp` | Sockets MPTCP (Multipath TCP). | `ss -M` |
| `--tipc` | Sockets TIPC. | `ss --tipc` |
| `--vsock` | Sockets VSOCK (VM ↔ host). | `ss --vsock` |
| `--xdp` | Sockets XDP (alias de `-f xdp`). | `ss --xdp` |
| `-f FAMILY`, `--family=FAMILY` | Familia explícita: `unix`, `inet`, `inet6`, `link`, `netlink`, `vsock`, `xdp`. | `ss -f inet6` |
| `-4`, `--ipv4` | Solo IPv4. | `ss -4` |
| `-6`, `--ipv6` | Solo IPv6. | `ss -6` |

---

## Filtrado de estado

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-l`, `--listening` | Solo sockets en escucha (LISTEN). | `ss -l` |
| `-a`, `--all` | Todos los sockets (en escucha + activos). | `ss -a` |
| `-B`, `--bound-inactive` | Sockets ligados pero inactivos. | `ss -B` |

---

## Información mostrada por socket

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-p`, `--processes` | Proceso dueño del socket (PID/nombre; root para todos). | `ss -tlnp` |
| `-e`, `--extended` | Información extendida (uid, inode, cookie). | `ss -e` |
| `-m`, `--memory` | Uso de memoria del socket (buffers). | `ss -tm` |
| `-i`, `--info` | Métricas internas TCP (rtt, cwnd, retransmisiones). | `ss -ti` |
| `-o`, `--options` | Información de temporizadores (timers). | `ss -to` |
| `-T`, `--threads` | Muestra hilos además de procesos (con `-p`). | `ss -tp -T` |
| `--tos` | Muestra ToS/priority del socket. | `ss --tos -t` |
| `--cgroup` | Muestra el cgroup del socket. | `ss --cgroup -t` |
| `--tipcinfo` | Información interna detallada de sockets TIPC. | `ss --tipc --tipcinfo` |
| `--inet-sockopt` | Muestra opciones de socket INET. | `ss -t --inet-sockopt` |
| `-Z`, `--context` | Contexto de seguridad SELinux. | `ss -Z` |
| `-z`, `--contexts` | Contexto de seguridad + proceso. | `ss -z` |

---

## Formato de salida

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-n`, `--numeric` | No resuelve nombres de servicio/host (más rápido). | `ss -tn` |
| `-r`, `--resolve` | Resuelve direcciones y puertos a nombres. | `ss -tr` |
| `-H`, `--no-header` | Suprime la línea de encabezado (scripting). | `ss -Htn` |
| `-O`, `--oneline` | Un socket por línea (evita saltos). | `ss -tO` |
| `-Q`, `--no-queues` | Omite las columnas Recv-Q / Send-Q. | `ss -tQ` |
| `--csv` | Salida en formato CSV. | `ss --csv -t` |

---

## Estadísticas y monitoreo

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-s`, `--summary` | Resumen/estadísticas globales de sockets por protocolo. | `ss -s` |
| `-E`, `--events` | Modo continuo: muestra sockets a medida que se destruyen. | `ss -E -t` |
| `-K`, `--kill` | **Cierra** por la fuerza los sockets que casen el filtro (requiere privilegios). | `ss -K dst 10.0.0.9` |

---

## Espacios de nombres, BPF y diagnóstico

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-N NSNAME`, `--net=NSNAME` | Ejecuta en otro network namespace. | `ss -N ns1 -t` |
| `-b`, `--bpf` | Muestra el filtro BPF asociado a los sockets. | `ss -0 -b` |
| `--bpf-maps` | Muestra los BPF maps asociados. | `ss --bpf-maps` |
| `--bpf-map-id=ID` | Filtra por un BPF map concreto. | `ss --bpf-map-id=42` |
| `-A QUERY`, `--query=QUERY` | Limita a familias/protocolos: `all`, `inet`, `tcp`, `udp`, `unix`... | `ss -A tcp,udp` |
| `-D FILE`, `--diag=FILE` | Vuelca la respuesta cruda de `sock_diag` a fichero. | `ss -t -D dump.bin` |
| `-F FILE`, `--filter=FILE` | Lee la expresión de filtro desde fichero. | `ss -t -F filtro.txt` |
| `-h`, `--help` | Ayuda. | `ss -h` |
| `-V`, `--version` | Versión. | `ss -V` |

---

## Lenguaje de filtros

### Por estado

| Expresión | Función | Ejemplo |
| --- | --- | --- |
| `state ESTABLISHED` | Conexiones establecidas. | `ss -t state ESTABLISHED` |
| `state LISTENING` | En escucha. | `ss -t state LISTENING` |
| `state TIME-WAIT` | En TIME-WAIT. | `ss -t state TIME-WAIT` |
| `state SYN-SENT` / `SYN-RECV` | Handshake en curso. | `ss -t state SYN-SENT` |
| `state FIN-WAIT-1` / `FIN-WAIT-2` | Cierre en curso. | `ss -t state FIN-WAIT-1` |
| `state CLOSE-WAIT` / `LAST-ACK` / `CLOSING` | Cierre pasivo/simultáneo. | `ss -t state CLOSE-WAIT` |
| `state connected` | Cualquier estado conectado (sinónimo agregado). | `ss -t state connected` |
| `state all` | Todos los estados. | `ss -t state all` |

### Por dirección / puerto

| Expresión | Función | Ejemplo |
| --- | --- | --- |
| `dst ADDR` | Filtra por destino. | `ss dst 10.0.0.9` |
| `src ADDR` | Filtra por origen. | `ss src 192.168.1.5` |
| `dport = :N` | Puerto destino. | `ss -t dport = :443` |
| `sport = :N` | Puerto origen. | `ss -t sport = :22` |
| `dport > :N` | Comparadores (`<`, `>`, `>=`, `<=`, `==`, `!=`). | `ss -t 'dport > :1024'` |
| combinadores | `and` / `or` / `not` para encadenar. | `ss -t 'dport = :80 or dport = :443'` |

---

## Estados TCP (referencia)

| Estado | Descripción |
| --- | --- |
| `LISTEN` | Escuchando conexiones entrantes. |
| `ESTABLISHED` | Conexión activa establecida. |
| `SYN-SENT` | Cliente envió SYN, espera SYN-ACK. |
| `SYN-RECV` | Servidor recibió SYN, envió SYN-ACK. |
| `FIN-WAIT-1` | Primer FIN enviado. |
| `FIN-WAIT-2` | ACK recibido del primer FIN. |
| `TIME-WAIT` | Espera a que expiren paquetes rezagados. |
| `CLOSE-WAIT` | Espera a que la aplicación local cierre. |
| `LAST-ACK` | Espera el ACK del FIN propio. |
| `CLOSING` | Ambos lados cerraron simultáneamente. |
| `CLOSED` | Conexión cerrada. |

---

## Casos prácticos

```bash
# Puertos en escucha con proceso, sin DNS (equivale a netstat -tlnp)
ss -tlnp

# Todas las conexiones a un host concreto
ss -tn dst 10.0.0.9

# Conexiones al puerto 443, formato de una línea sin cabecera
ss -Htn -O 'dport = :443'

# Cerrar por la fuerza todas las conexiones a una IP
ss -K dst 10.0.0.9

# Resumen global de sockets
ss -s

# Métricas TCP (rtt, cwnd) de sesiones establecidas
ss -ti state established
```

---

## Equivalencias con `netstat`

| netstat | ss |
| --- | --- |
| `netstat -tlnp` | `ss -tlnp` |
| `netstat -tunp` | `ss -tunp` |
| `netstat -s` | `ss -s` |
| `netstat -x` | `ss -x` |

Ver también [[netstat]] · [[ss]] (reemplazo moderno) · [[Red-Conectividad]] · [[tcpdump]].
