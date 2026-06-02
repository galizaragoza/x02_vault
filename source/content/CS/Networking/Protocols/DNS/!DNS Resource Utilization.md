#reference #Networking

**DNS Resource Utilization** = abuso del DNS como vector de **denegación de servicio** (DoS/DDoS), aprovechando que las respuestas pueden ser mucho mayores que las consultas y que el protocolo (UDP) permite spoofear el origen.

# Ataques

## DNS Amplification (reflexión)

El atacante envía consultas pequeñas con **IP de origen spoofeada** (la de la víctima) a resolvers abiertos. Estos responden con paquetes grandes **hacia la víctima** → amplificación.

```
Consulta:  60 bytes  (ANY / DNSSEC)  -> resolver abierto
Respuesta: ~4000 bytes               -> víctima (factor x50-x100)
```

| Concepto | Valor |
|----------|-------|
| Vector | UDP/53 con IP origen falsa |
| Amplificador | Open resolvers, ANY queries, DNSSEC (respuestas grandes) |
| Factor típico | x28 – x100 |

## NXDOMAIN / Water Torture

Inundar un resolver con consultas a **subdominios aleatorios inexistentes** → fuerza recursión hacia el autoritativo, agotando su CPU/conexiones y llenando la caché de NXDOMAIN.

## DNS Flood

Saturar directamente un servidor autoritativo con un volumen masivo de consultas.

# Mitigación

| Defensa | Efecto |
|---------|--------|
| **BCP38 / anti-spoofing** | El ISP filtra paquetes con origen falso (corta la reflexión). |
| Cerrar **open resolvers** | Elimina los amplificadores. |
| **Response Rate Limiting (RRL)** | Limita respuestas idénticas por cliente. |
| Anycast + sobredimensionado | Absorbe el volumen. |
| Restringir/limitar consultas `ANY` | Reduce el factor de amplificación. |

# Recursos
### [[DNS_CS]] · [[DNS Tunneling]]
### [Cloudflare — DNS amplification attack](https://www.cloudflare.com/learning/ddos/dns-amplification-ddos-attack/)
### [BCP38 — Network ingress filtering](https://www.rfc-editor.org/info/bcp38)
