#reference #Ciberseguridad

**Seguridad física** = controles que protegen el acceso físico a instalaciones, equipos y personas. Es la capa base: sin ella, los controles lógicos se eluden (robo de discos, evil maid, keyloggers HW).

# Sistemas de seguridad física

## Vallado y barreras
Perímetro disuasorio y de retardo: vallas, bolardos, mantraps (esclusas), torniquetes. Objetivo: **disuadir, retrasar, detectar**.

## Biométrica
Autenticación por características únicas: huella, iris, facial, voz. Métricas: **FAR** (falsos positivos), **FRR** (falsos negativos), **CER** (punto de cruce). Combinar con otro factor (algo que tienes/sabes).

## Control de acceso y badges
Tarjetas RFID/NFC, PIN, lectores. Riesgos: **clonado de tarjetas** (Proxmark), **tailgating/piggybacking** (colarse tras alguien). Mitigar con mantraps + concienciación.

## Vigilancia
CCTV, sensores de movimiento, alarmas, guardias, iluminación. Disuasión + evidencia forense. Logs de acceso correlacionables con eventos lógicos.

# Ataques físicos relevantes

| Ataque | Idea |
|--------|------|
| **Tailgating** | Entrar detrás de un autorizado. |
| **Evil maid** | Acceso breve al equipo → implante HW/SW. |
| **Lock picking / bypass** | Forzar cerraduras. |
| **Clonado de badges** | Copiar tarjetas RFID. |
| **Dumpster diving** | Buscar info en la basura → enlaza con [[OSINT_cheatsheet]]. |
| **USB drop** | Sembrar USB maliciosos. |

# Capas (defensa en profundidad)

Perímetro → edificio → sala → rack/equipo. Cada capa con su control. Complementa la seguridad lógica.

# Recursos
### [[Hardening]] · [[OSINT_cheatsheet]]
### [Wikipedia — Physical security](https://en.wikipedia.org/wiki/Physical_security)
