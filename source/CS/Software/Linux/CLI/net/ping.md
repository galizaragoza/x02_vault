ping envía paquetes ICMP ECHO_REQUEST a un host y mide el tiempo de respuesta. Es la herramienta más básica para verificar conectividad de red, medir latencia y detectar pérdida de paquetes. En Linux por defecto envía paquetes indefinidamente hasta que se interrumpe con Ctrl+C; en la mayoría de sistemas requiere permisos para ajustar opciones avanzadas como flood ping.

```
ping [opciones] <destino>
ping6 [opciones] <destino>
```

---

## Control de transmisión

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-c <N>` / `--count=<N>` | Envía N paquetes y termina. | `ping -c 4 8.8.8.8` |
| `-i <intervalo>` / `--interval=<intervalo>` | Intervalo en segundos entre paquetes (decimal). Por defecto: 1s. Valores < 0.2 requieren root. | `ping -i 0.5 host` |
| `-s <bytes>` / `--packet-size=<bytes>` | Tamaño del payload ICMP en bytes. Por defecto: 56 (= 64 con cabecera ICMP). | `ping -s 1400 host` |
| `-f` / `--flood` | Flood ping: envía paquetes lo más rápido posible (requiere root). Útil para pruebas de carga. | `ping -f host` |
| `-q` / `--quiet` | Silencia la salida línea a línea; solo muestra el resumen final. | `ping -q -c 10 host` |
| `-v` / `--verbose` | Salida detallada incluyendo respuestas ICMP que no son ECHO_REPLY. | `ping -v host` |

---

## Temporización

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-w <segundos>` / `--deadline=<segundos>` | Tiempo total máximo de ejecución en segundos. Sale aunque no se hayan enviado los N paquetes. | `ping -w 5 host` |
| `-W <segundos>` / `--timeout=<segundos>` | Timeout de espera por respuesta individual en segundos. | `ping -W 2 host` |

---

## Red y protocolo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-4` | Fuerza IPv4 aunque el destino tenga AAAA. | `ping -4 ejemplo.com` |
| `-6` | Fuerza IPv6. | `ping -6 ejemplo.com` |
| `-I <interfaz o IP>` | Usa la interfaz o IP de origen indicada. | `ping -I eth0 host` |
| `-n` | No resuelve las IPs a nombres DNS en la salida. | `ping -n 8.8.8.8` |
| `-t <TTL>` | Establece el TTL (Time To Live) de los paquetes enviados. | `ping -t 64 host` |
| `-T <opción>` | Opción de timestamp IP: `tsonly`, `tsandaddr`, `tsprespec`. | `ping -T tsandaddr host` |
| `-M <acción>` | Controla la fragmentación: `do` (no fragmentar, activa DF bit), `dont` (sin DF), `want` (PMTUD). Útil para descubrir MTU. | `ping -M do -s 1472 host` |
| `-b` | Permite hacer ping a direcciones de broadcast. | `ping -b 192.168.1.255` |

---

## Salida y registro

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-O` | Informa de ausencia de respuesta para cada paquete (muestra explícitamente los perdidos). | `ping -O host` |
| `-D` | Incluye timestamp (Unix epoch) antes de cada línea. | `ping -D host` |
| `-a` | Campana auditiva al recibir cada respuesta. | `ping -a host` |

---

## Casos de uso comunes

```bash
# Verificar conectividad básica
ping -c 4 8.8.8.8

# Verificar resolución DNS y conectividad
ping -c 3 google.com

# Medir latencia con intervalo más corto
ping -c 20 -i 0.2 host

# Solo resumen (sin líneas de respuesta)
ping -q -c 10 host

# Con deadline: si en 3s no responde, asume caído
ping -c 1 -W 1 -w 3 host && echo "UP" || echo "DOWN"

# Descubrir MTU del path (buscar tamaño sin fragmentación)
ping -M do -s 1472 -c 1 host

# Ping a través de interfaz específica
ping -I eth0 -c 4 10.0.0.1

# Flood (requiere root, para pruebas de red)
sudo ping -f -c 1000 host

# IPv6
ping -6 -c 4 ipv6.google.com

# Con timestamps (útil para correlacionar eventos)
ping -D host

# Broadcast (para descubrir hosts en red local)
sudo ping -b 192.168.1.255
```
