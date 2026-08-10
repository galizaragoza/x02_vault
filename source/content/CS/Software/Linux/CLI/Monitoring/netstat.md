netstat muestra conexiones de red, tablas de enrutamiento, estadísticas de interfaces y membresías multicast. Forma parte del paquete `net-tools` y está **obsoleto** en sistemas Linux modernos; su reemplazo es `ss` (del paquete `iproute2`), que es más rápido, más completo y usa la API del kernel directamente. Sin opciones muestra solo sockets activos (no en escucha).

```
netstat [opciones]
```

> Para nuevos scripts usar `ss` en lugar de `netstat`.

---

## Selección de sockets / protocolo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t` / `--tcp` | Muestra conexiones TCP. | `netstat -t` |
| `-u` / `--udp` | Muestra conexiones UDP. | `netstat -u` |
| `-w` / `--raw` | Muestra sockets RAW. | `netstat -w` |
| `-x` / `--unix` | Muestra sockets Unix de dominio. | `netstat -x` |
| `-l` / `--listening` | Muestra solo sockets en estado LISTEN (en escucha). | `netstat -tl` |
| `-a` / `--all` | Muestra todos los sockets: en escucha y activos. | `netstat -a` |
| `-A family` / `--protocol=family` | Filtra por familia: `inet`, `inet6`, `unix`, `ax25`, `ipx`... | `netstat -A inet` |
| `--inet` / `--ip` | Atajo para familia IPv4. | `netstat --inet -a` |
| `--inet6` | Atajo para familia IPv6. | `netstat --inet6 -a` |

---

## Salida y resolución de nombres

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n` / `--numeric` | Muestra IPs y puertos en formato numérico (sin resolver nombres). Más rápido. | `netstat -tn` |
| `--numeric-hosts` | No resuelve nombres de host, pero sí puertos. | `netstat -tl --numeric-hosts` |
| `--numeric-ports` | No resuelve nombres de puerto, pero sí hosts. | `netstat -tl --numeric-ports` |
| `-p` / `--program` | Muestra el PID y nombre del proceso dueño del socket (requiere root para ver todos). | `netstat -tlnp` |
| `-e` / `--extend` | Información extendida (usuario, inode). Repetible (`-ee`). | `netstat -te` |
| `-o` / `--timers` | Muestra los temporizadores de red asociados a cada socket. | `netstat -to` |
| `-N` / `--symbolic` | Resuelve identificadores de hardware a nombres simbólicos. | `netstat -N -i` |
| `-v` / `--verbose` | Salida detallada. | `netstat -v` |
| `-c` / `--continuous` | Actualiza la salida continuamente (cada segundo). | `netstat -c` |
| `-W` / `--wide` | No trunca direcciones IP en la salida. | `netstat -tlnpW` |
| `-T` / `--notrim` | No recorta las direcciones largas (variante según versión). | `netstat -T` |

---

## Tabla de enrutamiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r` / `--route` | Muestra la tabla de enrutamiento del kernel. Equivale a `route`. | `netstat -r` |
| `-C` / `--cache` | Muestra la cache de enrutamiento en vez de la FIB. | `netstat -rC` |
| `-F` / `--fib` | Muestra la FIB (Forwarding Information Base); es el default con `-r`. | `netstat -rF` |
| `-n` (con `-r`) | Muestra la tabla de rutas con IPs numéricas. | `netstat -rn` |

---

## Estadísticas, interfaces y NAT

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` / `--interfaces` | Muestra estadísticas de interfaces de red (paquetes TX/RX, errores). | `netstat -i` |
| `-s` / `--statistics` | Muestra estadísticas por protocolo (TCP, UDP, ICMP, IP). | `netstat -s` |
| `-g` / `--groups` | Muestra membresías de grupos multicast. | `netstat -g` |
| `-M` / `--masquerade` | Muestra las conexiones enmascaradas (NAT). | `netstat -M` |

---

## Ayuda

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-h` / `--help` | Muestra la ayuda de uso. | `netstat -h` |
| `-V` / `--version` | Muestra la versión. | `netstat -V` |

---

## Combinaciones frecuentes

```bash
# Puertos en escucha con proceso y sin resolución DNS
netstat -tlnp

# Todas las conexiones TCP activas
netstat -tn

# UDP en escucha
netstat -ulnp

# Tabla de rutas
netstat -rn

# Estadísticas por protocolo
netstat -s

# Estadísticas de interfaces
netstat -i

# Todos los sockets (TCP+UDP+Unix)
netstat -auntp

# Ver qué proceso usa un puerto específico
netstat -tlnp | grep :80
```

---

## Equivalencias con `ss` (reemplazo moderno)

| netstat | ss equivalente |
|---------|---------------|
| `netstat -tlnp` | `ss -tlnp` |
| `netstat -tunp` | `ss -tunp` |
| `netstat -rn` | `ip route` |
| `netstat -i` | `ip -s link` |
| `netstat -s` | `ss -s` |
