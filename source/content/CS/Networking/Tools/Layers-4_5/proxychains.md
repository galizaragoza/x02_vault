proxychains (y su reescritura proxychains-ng / proxychains4) es una herramienta que fuerza cualquier conexión TCP de un programa a través de uno o más proxies configurados, sin necesidad de modificar el binario. Funciona mediante LD_PRELOAD: intercepta las llamadas a la biblioteca de sockets de libc en tiempo de ejecución. Soporta cadenas de proxies SOCKS4, SOCKS5 y HTTP, en modos estricto, dinámico o aleatorio. Se usa en pruebas de penetración para anonimizar tráfico, pivotar a través de redes internas y evadir controles de IP.

```
proxychains [opciones] <comando> [args...]
proxychains4 [opciones] <comando> [args...]
```

---

## Opciones de línea de comandos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-f <archivo>` | Usa un fichero de configuración alternativo en lugar del por defecto. | `proxychains -f ~/custom.conf nmap -sT 10.0.0.1` |
| `-q` | Modo silencioso: suprime los mensajes de estado de conexión por proxy. | `proxychains -q curl http://example.com` |
| `--help` | Muestra ayuda. | `proxychains --help` |
| `--version` | Muestra versión. | `proxychains4 --version` |

---

## Fichero de configuración

Localización por defecto: `/etc/proxychains.conf` o `/etc/proxychains4.conf`. Alternativamente: `~/.proxychains/proxychains.conf`.

### Tipos de cadena (`chain_type`)

Solo puede estar activo uno a la vez (comentar los demás con `#`).

| Directiva | Descripción |
|-----------|-------------|
| `strict_chain` | Todos los proxies de la lista se usan en orden. Si uno falla, la conexión falla. |
| `dynamic_chain` | Usa los proxies en orden pero omite los que no responden. Requiere al menos uno operativo. |
| `random_chain` | Elige proxies al azar de la lista. Número de saltos controlado por `chain_len`. |
| `round_robin_chain` | Rota por los proxies en orden cíclico (solo proxychains-ng). |

### Otras directivas

| Directiva | Descripción | Ejemplo en config |
|-----------|-------------|-------------------|
| `chain_len <n>` | Número de proxies a usar en `random_chain`. | `chain_len = 2` |
| `proxy_dns` | Redirige las resoluciones DNS a través del proxy (evita DNS leaks). Activar siempre en uso anónimo. | `proxy_dns` |
| `tcp_read_time_out <ms>` | Timeout de lectura TCP en milisegundos. | `tcp_read_time_out 15000` |
| `tcp_connect_time_out <ms>` | Timeout de conexión TCP en milisegundos. | `tcp_connect_time_out 8000` |
| `quiet_mode` | Equivalente a `-q` pero en el fichero de config. Suprime mensajes. | `quiet_mode` |

### Formato de proxy en la lista

```
# tipo  host           puerto  [usuario  contraseña]
socks5  127.0.0.1      9050
socks4  10.0.0.1       1080
http    192.168.1.1    8080   usuario contraseña
socks5  proxy.ejemplo  9150   user     pass
```

| Tipo | Descripción |
|------|-------------|
| `socks4` | Proxy SOCKS4 (sin autenticación). |
| `socks5` | Proxy SOCKS5 (con o sin autenticación usuario:contraseña). |
| `http` | Proxy HTTP CONNECT (para tunelizar TCP; autenticación básica opcional). |

---

## Casos de uso comunes

```bash
# Navegar a través de Tor (requiere Tor escuchando en 127.0.0.1:9050)
proxychains firefox

# curl a través de cadena de proxies
proxychains curl http://ejemplo.com

# nmap TCP connect scan a través de proxy (nmap con proxychains: solo -sT funciona)
proxychains nmap -sT -p 22,80,443 10.10.10.1

# SSH a través de proxies
proxychains ssh usuario@10.10.10.1

# Modo silencioso (sin mensajes de proxy)
proxychains -q wget http://target.com/file

# Configuración alternativa (ej. para pivoting a red interna)
proxychains -f /tmp/internal_chain.conf nmap -sT 172.16.0.0/24

# Verificar que el proxy funciona
proxychains curl https://check.torproject.org
```

---

## Ejemplo de fichero de configuración

```ini
# /etc/proxychains.conf

dynamic_chain
# strict_chain
# random_chain

proxy_dns

tcp_read_time_out 15000
tcp_connect_time_out 8000

[ProxyList]
# Tor
socks5  127.0.0.1  9050

# Proxy intermedio
socks5  10.0.0.5   1080  user pass

# HTTP proxy corporativo
http    192.168.0.1 3128
```

---

## Limitaciones

- Solo funciona con programas que usen la libc estándar de sockets (no funciona con binarios estáticos, Go binaries, Rust sin libc, ni llamadas syscall directas).
- Con nmap solo es compatible `-sT` (TCP connect); los escaneos SYN (`-sS`) y UDP (`-sU`) no funcionan a través de proxychains.
- DNS over UDP no pasa por SOCKS; activar `proxy_dns` para usar DNS over TCP a través del proxy.
