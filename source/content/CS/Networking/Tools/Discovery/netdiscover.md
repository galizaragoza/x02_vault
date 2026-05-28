netdiscover es una herramienta de reconocimiento de red activo/pasivo basada en el protocolo ARP. En modo activo envía peticiones ARP a los hosts del rango objetivo y registra las respuestas; en modo pasivo escucha el tráfico ARP existente sin enviar paquetes. Es especialmente útil para descubrir hosts en redes conmutadas (switch) y redes inalámbricas donde herramientas basadas en ICMP pueden fallar. Requiere privilegios de root.

```
netdiscover [-i dispositivo] [-r rango | -l lista | -p] [-m mac_list] [-F filtro] [-s tiempo] [-c número] [-n nodo] [-dfPLNS]
```

---

## Selección de objetivo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-r <rango>` | Escanea el rango de red indicado en notación CIDR. Sin esta opción escanea rangos privados comunes (192.168.0.0/16, 172.16.0.0/12, etc.). | `netdiscover -r 192.168.1.0/24` |
| `-l <archivo>` | Lee rangos de red desde un archivo (uno por línea). | `netdiscover -l rangos.txt` |
| `-p` | Modo pasivo: solo escucha tráfico ARP, no envía peticiones. Más sigiloso pero más lento. | `netdiscover -p -i eth0` |

---

## Interfaz y red

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i <interfaz>` | Interfaz de red a usar. Por defecto: primera interfaz encontrada. | `netdiscover -i wlan0` |
| `-n <nodo>` | Último octeto de la IP local cuando hay múltiples IPs en la interfaz. | `netdiscover -n 5 -r 10.0.0.0/24` |
| `-F <filtro>` | Filtro pcap adicional para el modo pasivo. | `netdiscover -p -F "arp"` |

---

## Control de escaneo y rendimiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-s <ms>` | Tiempo de espera en milisegundos entre cada petición ARP enviada. | `netdiscover -s 500 -r 192.168.1.0/24` |
| `-c <número>` | Número de veces que se envía cada petición ARP por host. Más repeticiones = mayor fiabilidad. Por defecto: 1. | `netdiscover -c 3 -r 10.0.0.0/24` |
| `-f` | Modo rápido: escanea solo 1/256 del rango para prueba rápida. | `netdiscover -f -r 172.16.0.0/12` |
| `-d` | No activa sleep entre peticiones (ignora `-s`). Puede saturar la red. | `netdiscover -d -r 192.168.1.0/24` |

---

## Salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-P` | Modo print: salida para pipes/scripts (no interactiva). Imprime resultados conforme se descubren. | `netdiscover -P -r 192.168.1.0/24` |
| `-L` | Muestra la tabla de hosts ordenada al terminar (en lugar de actualización en tiempo real). | `netdiscover -L -r 192.168.0.0/24` |
| `-N` | No muestra banner/cabecera. | `netdiscover -N -r 192.168.1.0/24` |
| `-S` | Muestra al finalizar las estadísticas de la sesión. | `netdiscover -S -r 10.0.0.0/24` |
| `-m <archivo>` | Carga una lista de MACs conocidas para compararlas con los hosts descubiertos. | `netdiscover -m known_macs.txt -r 192.168.1.0/24` |

---

## Casos de uso comunes

```bash
# Descubrir hosts en la red local (interfaz automática)
netdiscover -r 192.168.1.0/24

# Especificar interfaz
netdiscover -i eth0 -r 10.0.0.0/24

# Modo pasivo (sin enviar paquetes)
netdiscover -p -i wlan0

# Modo rápido y silencioso para scripting
netdiscover -P -N -r 192.168.1.0/24

# Escaneo más fiable enviando 3 peticiones por host
netdiscover -c 3 -r 192.168.0.0/24

# Descubrir en redes inalámbricas (monitor mode no requerido, modo pasivo)
netdiscover -p -i wlan0 -N
```
