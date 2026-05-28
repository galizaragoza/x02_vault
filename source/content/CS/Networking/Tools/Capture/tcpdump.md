tcpdump es un analizador de paquetes de red por línea de comandos que captura y muestra tráfico en tiempo real o desde archivos de captura. Utiliza la biblioteca libpcap y admite el lenguaje de filtros BPF (Berkeley Packet Filter) para inspección granular del tráfico. Es la herramienta estándar de facto para análisis de tráfico, diagnóstico de red e investigación de incidentes de seguridad.

```
tcpdump [opciones] [expresión-filtro]
```

---

## Selección de interfaz

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i <iface>` | Especifica la interfaz de captura. | `tcpdump -i eth0` |
| `-i any` | Captura en todas las interfaces disponibles. | `tcpdump -i any` |
| `-D` | Lista todas las interfaces disponibles y sus índices. | `tcpdump -D` |

---

## Control de captura

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-c <n>` | Para la captura tras recibir `n` paquetes. | `tcpdump -c 100 -i eth0` |
| `-s <snaplen>` | Longitud máxima de captura por paquete en bytes. `0` captura el paquete completo. | `tcpdump -s 0 -i eth0` |
| `-G <secs>` | Rota el archivo de salida cada `secs` segundos (requiere `-w`). | `tcpdump -G 60 -w cap_%H%M.pcap` |
| `-W <n>` | Limita el número de archivos de rotación a `n` (requiere `-G`). | `tcpdump -G 60 -W 10 -w cap_%M.pcap` |
| `-C <n>` | Rota el archivo cuando supera `n` MB (requiere `-w`). | `tcpdump -C 10 -w captura.pcap` |
| `--count` | Muestra el número de paquetes capturados y sale. | `tcpdump --count -r captura.pcap` |

---

## Salida y formato

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v` | Verbose: muestra TTL, ID IP, opciones. | `tcpdump -v -i eth0` |
| `-vv` | Verbose mayor: decodifica campos adicionales de protocolos. | `tcpdump -vv -i eth0` |
| `-vvv` | Verbose máximo: decodificación completa. | `tcpdump -vvv -i eth0` |
| `-q` | Modo silencioso: reduce la salida por paquete. | `tcpdump -q -i eth0` |
| `-e` | Muestra la cabecera de enlace de datos (MAC origen/destino). | `tcpdump -e -i eth0` |
| `-A` | Muestra el payload del paquete en texto ASCII. | `tcpdump -A port 80` |
| `-X` | Muestra el payload en hexadecimal y ASCII. | `tcpdump -X port 443` |
| `-XX` | Como `-X` pero incluyendo la cabecera Ethernet. | `tcpdump -XX -i eth0` |
| `-x` | Muestra el payload solo en hexadecimal. | `tcpdump -x -i eth0` |
| `-S` | Muestra números de secuencia TCP absolutos. | `tcpdump -S -i eth0 tcp` |
| `-l` | Vuelca la salida línea a línea (útil para pipes en tiempo real). | `tcpdump -l -i eth0 \| grep "GET"` |
| `-t` | No imprime timestamp. | `tcpdump -t -i eth0` |
| `-tt` | Imprime timestamp Unix sin formato. | `tcpdump -tt -i eth0` |
| `-ttt` | Imprime delta de tiempo entre paquetes consecutivos. | `tcpdump -ttt -i eth0` |
| `-tttt` | Imprime timestamp en formato fecha y hora. | `tcpdump -tttt -i eth0` |
| `-ttttt` | Imprime delta desde el primer paquete capturado. | `tcpdump -ttttt -i eth0` |
| `-#` | Muestra el número de paquete. | `tcpdump -# -i eth0` |
| `--print` | Imprime los paquetes incluso cuando se escribe a archivo. | `tcpdump --print -w cap.pcap` |

---

## Resolución de nombres

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n` | No resuelve IPs a hostnames. | `tcpdump -n -i eth0` |
| `-nn` | No resuelve IPs ni números de puerto a nombres. | `tcpdump -nn -i eth0` |
| `-N` | No imprime el dominio completo, solo el hostname. | `tcpdump -N -i eth0` |

---

## Lectura y escritura de archivos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-w <file>` | Guarda los paquetes en un archivo pcap. | `tcpdump -i eth0 -w captura.pcap` |
| `-r <file>` | Lee paquetes de un archivo pcap. | `tcpdump -r captura.pcap` |
| `-F <file>` | Lee la expresión de filtro desde un archivo. | `tcpdump -F filtros.bpf -i eth0` |
| `-V <file>` | Lee una lista de archivos pcap desde un fichero de texto. | `tcpdump -V lista.txt` |
| `--immediate-mode` | Entrega paquetes sin buffering de kernel. | `tcpdump --immediate-mode -i eth0` |

---

## Filtros BPF — por host y red

| Expresión | Descripción | Ejemplo |
|-----------|-------------|---------|
| `host <IP>` | Tráfico con esa IP como origen o destino. | `tcpdump host 192.168.1.1` |
| `src host <IP>` | Solo paquetes con esa IP como origen. | `tcpdump src host 10.0.0.5` |
| `dst host <IP>` | Solo paquetes con esa IP como destino. | `tcpdump dst host 8.8.8.8` |
| `net <red>` | Tráfico de una red (notación CIDR o máscara). | `tcpdump net 192.168.0.0/24` |
| `src net <red>` | Red de origen. | `tcpdump src net 10.0.0.0/8` |
| `dst net <red>` | Red de destino. | `tcpdump dst net 172.16.0.0/12` |
| `ether host <MAC>` | Filtra por dirección MAC. | `tcpdump ether host aa:bb:cc:dd:ee:ff` |
| `broadcast` | Captura paquetes broadcast. | `tcpdump broadcast` |
| `multicast` | Captura paquetes multicast. | `tcpdump multicast` |

---

## Filtros BPF — por puerto

| Expresión | Descripción | Ejemplo |
|-----------|-------------|---------|
| `port <n>` | Puerto `n` en origen o destino. | `tcpdump port 80` |
| `src port <n>` | Puerto de origen. | `tcpdump src port 443` |
| `dst port <n>` | Puerto de destino. | `tcpdump dst port 22` |
| `portrange <a>-<b>` | Rango de puertos. | `tcpdump portrange 1-1024` |

---

## Filtros BPF — por protocolo

| Expresión | Descripción | Ejemplo |
|-----------|-------------|---------|
| `tcp` | Solo tráfico TCP. | `tcpdump tcp` |
| `udp` | Solo tráfico UDP. | `tcpdump udp` |
| `icmp` | Solo tráfico ICMP. | `tcpdump icmp` |
| `icmp6` | Solo tráfico ICMPv6. | `tcpdump icmp6` |
| `arp` | Solo tráfico ARP. | `tcpdump arp` |
| `ip` | Solo paquetes IPv4. | `tcpdump ip` |
| `ip6` | Solo paquetes IPv6. | `tcpdump ip6` |
| `proto <n>` | Filtra por número de protocolo IP. | `tcpdump proto 50` |

---

## Filtros BPF — operadores lógicos

| Expresión | Descripción | Ejemplo |
|-----------|-------------|---------|
| `and` / `&&` | AND lógico entre expresiones. | `tcpdump host 10.0.0.1 and port 80` |
| `or` / `\|\|` | OR lógico. | `tcpdump port 80 or port 443` |
| `not` / `!` | Niega la expresión. | `tcpdump not port 22` |
| `( )` | Agrupación de expresiones (escapar en shell). | `tcpdump '(port 80 or port 443) and host 10.0.0.1'` |

---

## Filtros BPF — flags TCP

| Expresión | Descripción | Ejemplo |
|-----------|-------------|---------|
| `tcp[tcpflags] & tcp-syn != 0` | Paquetes con flag SYN activo. | `tcpdump 'tcp[tcpflags] & tcp-syn != 0'` |
| `tcp[tcpflags] & tcp-ack != 0` | Paquetes con flag ACK activo. | `tcpdump 'tcp[tcpflags] & tcp-ack != 0'` |
| `tcp[tcpflags] & tcp-rst != 0` | Paquetes con flag RST. | `tcpdump 'tcp[tcpflags] & tcp-rst != 0'` |
| `tcp[tcpflags] & tcp-fin != 0` | Paquetes con flag FIN. | `tcpdump 'tcp[tcpflags] & tcp-fin != 0'` |
| `tcp[tcpflags] == tcp-syn` | Solo SYN puro (inicio de conexión, sin ACK). | `tcpdump 'tcp[tcpflags] == tcp-syn'` |

---

## Filtros BPF — acceso directo a bytes

| Expresión | Descripción | Ejemplo |
|-----------|-------------|---------|
| `proto[offset:size]` | Accede a `size` bytes en `offset` del protocolo. | `tcpdump 'ip[8] < 10'` (TTL < 10) |
| `ip[6:2] & 0x1fff != 0` | Detecta fragmentos IP. | `tcpdump 'ip[6:2] & 0x1fff != 0'` |

---

## Casos de uso comunes

```bash
# Captura HTTP y muestra payload en ASCII
tcpdump -i eth0 -A -s 0 port 80

# Captura todo el tráfico excepto SSH
tcpdump -i eth0 -w trafico.pcap not port 22

# Monitoriza conexiones nuevas (solo SYN puro)
tcpdump -nn -i eth0 'tcp[tcpflags] == tcp-syn'

# Captura tráfico DNS
tcpdump -n -i eth0 port 53

# Lee pcap y filtra por IP
tcpdump -r captura.pcap host 10.0.0.5

# Detecta escaneos SYN (SYN sin ACK de respuesta)
tcpdump -nn -i eth0 'tcp[tcpflags] & tcp-syn != 0 and tcp[tcpflags] & tcp-ack == 0'

# Captura con rotación cada 5 min, máximo 6 archivos (30 min total)
tcpdump -i eth0 -G 300 -W 6 -w /tmp/cap_%Y%m%d_%H%M.pcap
```
