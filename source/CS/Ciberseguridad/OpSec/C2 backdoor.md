#reference #Ciberseguridad #OpSec

Una reverse shell cruda es un **suicidio de OpSec** (sin cifrado, sin persistencia, sin estabilidad, ruidosa). En engagements reales se usa una infraestructura **C2** (Command & Control): un servidor que gestiona implantes/beacons de forma cifrada y sigilosa.

![[c2.jpg]]

# Conceptos

| Término | Significado |
|---------|-------------|
| **C2 server / team server** | Servidor central que controla los implantes. |
| **Beacon / implant / agent** | Código en la víctima que llama a casa periódicamente. |
| **Listener** | Endpoint del C2 que recibe los check-ins. |
| **Callback / check-in** | El beacon contacta al C2 (pull), no al revés → evade firewalls de entrada. |
| **Sleep / jitter** | Intervalo + aleatoriedad entre check-ins para evadir detección por patrón. |
| **Redirector** | Proxy intermedio (nginx, CDN, domain fronting) que oculta la IP real del C2. |
| **Malleable profile** | Perfil que disfraza el tráfico C2 como tráfico legítimo (ej. simular API de Microsoft). |

# Canales C2

| Canal | Nota |
|-------|------|
| HTTP/HTTPS | El más común; se mezcla con tráfico web. |
| DNS | Exfil/control vía consultas DNS — evade muchos firewalls ([[DNS_CS]] tunneling). |
| SMB / named pipes | C2 lateral entre hosts internos (peer-to-peer). |
| ICMP | Encubierto, bajo ancho de banda. |

# OpSec del operador

- **Redirectors + domain fronting** → nunca exponer la IP del team server.
- **Jitter + sleep largo** → evitar beaconing periódico detectable.
- **Tráfico cifrado y malleable** → parecer HTTPS legítimo.
- Categorizar el dominio C2 (no usar dominios recién registrados / mal reputados).
- Limpiar artefactos; no reutilizar IOCs entre campañas.

# Frameworks

| Framework | Nota |
|-----------|------|
| **Cobalt Strike** | Comercial, estándar de la industria red team. |
| **Sliver** | Open source, moderno, multiplataforma. |
| **Mythic** | Modular, multi-agente, web UI. |
| **Havoc** | Open source moderno → ver [[Havoc]]. |
| **Covenant / Merlin** | .NET / Go → ver [[Merlin]]. |
| **Metasploit** | `msfconsole` para C2 básico / payloads. |

> Uso exclusivo en engagements autorizados (pentest/red team) o labs propios.

# Recursos
### [[Havoc]] · [[Merlin]] · [[Tor]]
### [Getting Started With C2 Servers — Covenant](https://youtu.be/BLMW0fougFM?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
### [Sliver](https://github.com/BishopFox/sliver) · [Mythic](https://github.com/its-a-feature/Mythic)
