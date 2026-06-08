#reference #Networking

**RTP** (Real-time Transport Protocol) = protocolo para transportar **audio/vídeo en tiempo real** (VoIP, videollamadas, streaming). Va sobre **UDP** (latencia mínima, tolera pérdidas). Se acompaña de **RTCP** para estadísticas de calidad y de **SIP** para señalización.

# Pila típica (VoIP)

| Protocolo | Rol |
|-----------|-----|
| **SIP** | Señalización: iniciar/terminar llamadas (puertos 5060/5061). |
| **RTP** | Transporte del media (audio/vídeo); rango UDP alto (10000–20000). |
| **RTCP** | Control/calidad de la sesión RTP (QoS, jitter, pérdida). |
| **SRTP** | RTP **cifrado** (confidencialidad + integridad). |

# Cabecera RTP (campos clave)

| Campo | Función |
|-------|---------|
| **Payload type** | Códec (PCMU, G.711, Opus…). |
| **Sequence number** | Detectar pérdidas y reordenar. |
| **Timestamp** | Sincronizar reproducción. |
| **SSRC** | Identificador de la fuente. |

# Relevancia en seguridad

- **Sniffing de RTP** sin cifrar → reconstruir la conversación. Wireshark: *Telephony → RTP → Play Streams*.
- **VoIP pentest**: enumerar SIP (`svwar`, `sipvicious`), interceptar RTP, ataques a SIP (registro, fuzzing).
- Mitigación: **SRTP** + TLS para SIP (cifrado extremo a extremo del media y la señalización).

```bash
# Capturar y extraer audio RTP
tshark -i eth0 -f "udp portrange 10000-20000" -w rtp.pcap
# Wireshark: RTP -> Analyse -> Play / export como .wav
```

# Recursos
### [[IP_CS]]
### [RFC 3550 — RTP](https://www.rfc-editor.org/rfc/rfc3550) · [SIPVicious](https://github.com/EnableSecurity/sipvicious)
