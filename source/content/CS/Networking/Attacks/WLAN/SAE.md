#reference #Networking

**SAE** (Simultaneous Authentication of Equals), aka **Dragonfly**, es el handshake de **WPA3-Personal** que sustituye al PSK de WPA2. Aporta **forward secrecy** y resistencia a ataques de diccionario **offline**: capturar el handshake ya no permite crackear la contraseña sin interactuar con el AP.

# Qué mejora frente a WPA2

| Propiedad | WPA2 (PSK) | WPA3 (SAE) |
|-----------|-----------|------------|
| Crack offline del handshake | ✅ posible (Hashcat) | ❌ no (cada intento requiere el AP) |
| Forward secrecy | ❌ | ✅ (clave de sesión efímera) |
| Protección de gestión | opcional | **PMF obligatorio** |

> SAE es un **PAKE** (Password Authenticated Key Exchange) basado en Diffie-Hellman sobre curva elíptica: ambos extremos prueban conocer la contraseña sin transmitir nada crackeable offline.

# Ataques conocidos: Dragonblood (2019)

Vulnerabilidades de implementación/diseño de SAE:

| Ataque | Idea |
|--------|------|
| **Side-channel (cache/timing)** | Fugas durante el *hunting-and-pecking* revelan info de la password → diccionario parcial. |
| **Downgrade a WPA2** | Modo transición WPA3/WPA2 → forzar el cliente a WPA2 y capturar handshake clásico. |
| **DoS por sobrecarga** | El cómputo de SAE es caro → inundar de commits agota el AP. |

# Relevancia ofensiva

- WPA3 puro **rompe** el flujo clásico de captura + Hashcat ([[KRACK]] y crack de PSK).
- En la práctica muchas redes usan **modo transición** → siguen siendo atacables vía downgrade a WPA2.
- Mitigación: WPA3-only (sin transición), implementaciones parcheadas contra Dragonblood, contraseñas fuertes igualmente.

# Recursos
### [[KRACK]]
### [Dragonblood — wpa3.mathyvanhoef.com](https://wpa3.mathyvanhoef.com/)
### [Wikipedia — SAE / Dragonfly](https://en.wikipedia.org/wiki/Simultaneous_Authentication_of_Equals)
