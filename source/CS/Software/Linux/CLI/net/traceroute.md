traceroute descubre la ruta que siguen los paquetes IP desde el host local hasta un destino, identificando cada salto intermedio (router). Funciona enviando paquetes con TTL creciente y registrando los mensajes ICMP "Time Exceeded" que devuelven los routers intermedios. Por defecto usa datagramas UDP con puertos altos en Linux; puede cambiarse a ICMP o TCP para evadir firewalls. Requiere privilegios de root para los modos ICMP y TCP.

```
traceroute [opciones] <host> [longitud_paquete]
```

---

## Protocolo de sondeo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| (por defecto) | UDP a puertos altos (33434+). No requiere root. Puede ser bloqueado por firewalls. | `traceroute 8.8.8.8` |
| `-I` / `--icmp` | ICMP ECHO request (como ping). Más compatible con routers. Requiere root. | `traceroute -I 8.8.8.8` |
| `-T` / `--tcp` | TCP SYN al puerto indicado (por defecto 80). Útil para traversal de firewalls. Requiere root. | `traceroute -T 8.8.8.8` |
| `-U` / `--udp` | UDP al puerto fijo indicado con `-p` (a diferencia del modo UDP por defecto que incrementa el puerto). | `traceroute -U -p 53 8.8.8.8` |
| `-p <puerto>` / `--port=<puerto>` | Puerto de destino (para modos TCP y UDP fijo). | `traceroute -T -p 443 8.8.8.8` |
| `--sport=<puerto>` | Puerto de origen (solo modos TCP/UDP). | `traceroute -T --sport=12345 -p 80 8.8.8.8` |

---

## Control de saltos y TTL

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-m <n>` / `--max-hops=<n>` | Número máximo de saltos (TTL máximo). Por defecto: 30. | `traceroute -m 15 8.8.8.8` |
| `-f <n>` / `--first=<n>` | TTL inicial (primer salto a mostrar). Permite empezar desde un salto concreto. | `traceroute -f 5 8.8.8.8` |
| `-q <n>` / `--queries=<n>` | Número de sondeos por salto. Por defecto: 3. | `traceroute -q 5 8.8.8.8` |

---

## Temporización

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-w <n>` / `--wait=<n>` | Tiempo máximo de espera de respuesta por sondeo, en segundos. Por defecto: 5. | `traceroute -w 2 8.8.8.8` |
| `-z <n>` / `--sendwait=<n>` | Tiempo mínimo entre envíos de sondeos en ms (o segundos si `>= 10`). Evita rate limiting en routers. | `traceroute -z 100 8.8.8.8` |
| `-N <n>` / `--sim-queries=<n>` | Número de sondeos simultáneos. Por defecto: 16. Reducir para menor carga. | `traceroute -N 3 8.8.8.8` |

---

## Origen y red

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i <dispositivo>` / `--interface=<dispositivo>` | Interfaz de red de origen a usar. | `traceroute -i eth0 8.8.8.8` |
| `-s <dirección>` / `--source=<dirección>` | Dirección IP de origen (debe pertenecer al host). | `traceroute -s 192.168.1.5 8.8.8.8` |
| `-g <gateway>` | Loose source routing: añade gateway al path (IPv4, máx. 8 entradas). | `traceroute -g 10.0.0.1 8.8.8.8` |
| `--mtu` | Descubre el MTU del path (usa paquetes grandes). | `traceroute --mtu 8.8.8.8` |

---

## Resolución de nombres y salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n` | No resuelve direcciones IP a nombres de dominio. Más rápido. | `traceroute -n 8.8.8.8` |
| `-A` / `--as-path-lookups` | Muestra el número de AS (Autonomous System) para cada salto. Requiere whois. | `traceroute -A 8.8.8.8` |
| `-l` | Muestra el TTL del paquete de respuesta recibido. | `traceroute -l 8.8.8.8` |
| `-e` / `--extensions` | Muestra extensiones ICMP (ej. información de MPLS). | `traceroute -e 8.8.8.8` |
| `--sport` | (Ver sección protocolo arriba.) | — |
| `-d` / `--debug` | Activa debug a nivel de socket. | `traceroute -d 8.8.8.8` |
| `--back` | Imprime el número de saltos de vuelta (si difiere del de ida). | `traceroute --back 8.8.8.8` |

---

## Tamaño de paquete

| Parámetro | Descripción | Ejemplo |
|-----------|-------------|---------|
| `<longitud>` | Tamaño del paquete UDP/ICMP en bytes (argumento posicional final). Por defecto: 60. | `traceroute 8.8.8.8 100` |

---

## Casos de uso comunes

```bash
# Traza básica
traceroute 8.8.8.8

# Sin resolución DNS (más rápido)
traceroute -n 8.8.8.8

# ICMP (como ping, más información en algunos routers)
traceroute -I google.com

# TCP SYN al puerto 443 (para evadir firewalls que bloquean UDP)
sudo traceroute -T -p 443 8.8.8.8

# Solo los primeros 10 saltos
traceroute -m 10 8.8.8.8

# Empezar desde el salto 5 (skippear LAN conocida)
traceroute -f 5 8.8.8.8

# Con información de AS para cada salto
traceroute -A 8.8.8.8

# Interfaz específica
traceroute -i wlan0 8.8.8.8

# Menos sondeos por salto para traza más rápida
traceroute -q 1 -n 8.8.8.8
```
