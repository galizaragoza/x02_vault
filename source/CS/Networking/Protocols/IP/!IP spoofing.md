#reference #Networking

**IP Spoofing** = falsificar la **dirección IP de origen** de un paquete para hacerse pasar por otro host, ocultar el origen real o eludir controles basados en IP. Posible porque la cabecera IP no autentica el origen.

# Usos

| Objetivo | Cómo ayuda |
|----------|-----------|
| **DDoS por reflexión/amplificación** | Origen = IP de la víctima → las respuestas la inundan (ver [[!DNS Resource Utilization]]). |
| **Ocultar el origen** | El destino no ve la IP real del atacante. |
| **Bypass de ACL/IP allowlist** | Suplantar una IP de confianza. |
| **Session hijacking (clásico)** | Suplantar a un host en una conexión TCP. |

# Límite con TCP

TCP usa un handshake de 3 vías con **números de secuencia**. Con la IP spoofeada, las respuestas (SYN-ACK) van a la IP falsificada, no al atacante → **conexión a ciegas**: hay que **adivinar el ISN** (número de secuencia inicial). Por eso el spoofing es fácil en protocolos **sin conexión (UDP, ICMP)** y difícil en TCP establecido.

```bash
# Forjar paquetes con scapy
from scapy.all import *
send(IP(src="1.2.3.4", dst="victima")/ICMP())          # ping spoofeado
send(IP(src="1.2.3.4", dst="victima")/UDP(dport=53)/DNS())
# hping3
hping3 --spoof 1.2.3.4 -S -p 80 victima
```

# Mitigación

| Defensa | Efecto |
|---------|--------|
| **BCP38 / ingress filtering** | El ISP descarta paquetes con origen imposible. |
| **uRPF** (Reverse Path Forwarding) | Verifica que el origen sea alcanzable por esa interfaz. |
| ISN aleatorio | Dificulta el spoofing TCP a ciegas. |
| Autenticación de capa superior (IPsec, TLS) | La IP deja de ser la base de confianza. |

# Recursos
### [[IP_CS]] · [[!DNS Resource Utilization]] · [[ARP Cache Poisoning]]
### [Wikipedia — IP address spoofing](https://en.wikipedia.org/wiki/IP_address_spoofing) · [BCP38](https://www.rfc-editor.org/info/bcp38)
