#reference #Ciberseguridad #OpSec

**Tor** (The Onion Router) = red de anonimato que enruta el tráfico por **3 relays** cifrando en capas (*onion routing*). Cada nodo solo conoce al anterior y al siguiente, nunca origen y destino a la vez.

![[tor.png|755x424]]

# Circuito

| Nodo | Conoce | No conoce |
|------|--------|-----------|
| **Guard / Entry** | Tu IP real | El destino |
| **Middle** | Entry y Exit | Ni tu IP ni el destino |
| **Exit** | El destino + tráfico (si no cifrado) | Tu IP real |

> El **exit node ve el tráfico en claro** si no hay TLS extremo a extremo. Usa siempre HTTPS sobre Tor. Para servicios `.onion` el cifrado es de extremo a extremo y no hay exit node.

# Conceptos

| Término | Significado |
|---------|-------------|
| **Onion service** (`.onion`) | Servicio accesible solo dentro de Tor; oculta también la IP del servidor. |
| **Bridge** | Relay no listado, para evadir bloqueos de Tor. |
| **Pluggable transport** | Ofusca el tráfico Tor (`obfs4`, `meek`, `snowflake`) frente a DPI. |
| **Guard fijo** | El nodo de entrada se mantiene un tiempo para resistir ataques de correlación. |

# Uso

```bash
# Servicio Tor + proxy SOCKS5 local en 9050
sudo systemctl start tor

# Enrutar una herramienta por Tor
torsocks curl https://check.torproject.org
proxychains nmap -sT -Pn target.onion       # ProxyChains -> SOCKS 9050

# curl directo al SOCKS
curl --socks5-hostname 127.0.0.1:9050 https://ifconfig.me
```

`/etc/proxychains.conf` → `socks5 127.0.0.1 9050`.

# Límites de anonimato (OpSec)

- Correlación de tráfico por un adversario global (entrada+salida temporizadas).
- Fugas fuera del proxy: DNS, WebRTC, metadatos, login con identidad real.
- `nmap -sU`/escaneos UDP/ICMP **no** pasan por SOCKS TCP → fugan tu IP. Usa `-sT -Pn`.
- No mezclar identidad real y anónima en el mismo circuito/navegador.

> Tor anonimiza la **ruta**, no tu comportamiento. Un login, una cookie o un escaneo mal configurado te desanonimizan.

# Recursos
### [[C2 backdoor]]
### [Tor Project](https://www.torproject.org/) · [How TOR works](https://youtu.be/79m7mX3rC8Q)
