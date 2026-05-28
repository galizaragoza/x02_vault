netstat muestra conexiones de red, tablas de enrutamiento, estadísticas de interfaces y membresías multicast. Forma parte del paquete `net-tools` y está **obsoleto** en sistemas Linux modernos; su reemplazo es `ss` (del paquete `iproute2`), que es más rápido, más completo y usa la API del kernel directamente. Sin opciones muestra solo sockets activos (no en escucha).

```
netstat [opciones]
```

> Para nuevos scripts usar `ss` en lugar de `netstat`.

---

## Selección de sockets

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t` / `--tcp` | Muestra conexiones TCP. | `netstat -t` |
| `-u` / `--udp` | Muestra conexiones UDP. | `netstat -u` |
| `-x` / `--unix` | Muestra sockets Unix de dominio. | `netstat -x` |
| `-l` / `--listening` | Muestra solo sockets en estado LISTEN (en escucha). | `netstat -tl` |
| `-a` / `--all` | Muestra todos los sockets: en escucha y activos. | `netstat -a` |

---

## Salida y resolución de nombres

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n` / `--numeric` | Muestra IPs y puertos en formato numérico (sin resolver nombres). Más rápido. | `netstat -tn` |
| `--numeric-hosts` | No resuelve nombres de host, pero sí puertos. | `netstat -tl --numeric-hosts` |
| `--numeric-ports` | No resuelve nombres de puerto, pero sí hosts. | `netstat -tl --numeric-ports` |
| `-p` / `--programs` | Muestra el PID y nombre del proceso dueño del socket (requiere root para ver todos). | `netstat -tlnp` |
| `-e` / `--extend` | Información extendida (usuario, inode). | `netstat -te` |
| `-v` / `--verbose` | Salida detallada. | `netstat -v` |
| `-c` / `--continuous` | Actualiza la salida continuamente. | `netstat -c` |
| `-W` / `--wide` | No trunca direcciones IP en la salida. | `netstat -tlnpW` |

---

## Tabla de enrutamiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r` / `--route` | Muestra la tabla de enrutamiento del kernel. Equivale a `route`. | `netstat -r` |
| `-n` (con `-r`) | Muestra la tabla de rutas con IPs numéricas. | `netstat -rn` |

---

## Estadísticas e interfaces

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` / `--interfaces` | Muestra estadísticas de interfaces de red (paquetes TX/RX, errores). | `netstat -i` |
| `-s` / `--statistics` | Muestra estadísticas por protocolo (TCP, UDP, ICMP, IP). | `netstat -s` |
| `-g` / `--groups` | Muestra membresías de grupos multicast. | `netstat -g` |

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
