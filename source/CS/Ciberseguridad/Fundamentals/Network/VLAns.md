#reference #Ciberseguridad #Networking

**VLAN** (Virtual LAN) = segmentación **lógica** de una red física en dominios de difusión separados, sin necesidad de hardware aparte. Cada VLAN agrupa hosts como si estuvieran en su propia red, aunque compartan el mismo switch.

# Segmentation

Separar la red en VLANs limita el alcance de broadcast y **contiene** ataques: un host comprometido en una VLAN no ve el tráfico de otras (salvo enrutamiento explícito).

| Beneficio | Detalle |
|-----------|---------|
| Aislamiento | Departamentos/IoT/invitados separados. |
| Reducción de broadcast | Menos ruido, mejor rendimiento. |
| Seguridad | Limita movimiento lateral; políticas por VLAN. |
| Flexibilidad | Agrupación lógica independiente de la ubicación física. |

# Conceptos

| Término | Significado |
|---------|-------------|
| **802.1Q** | Estándar de etiquetado (tag) de tramas con el VLAN ID. |
| **Access port** | Puerto en una sola VLAN (hacia un host). |
| **Trunk port** | Puerto que transporta varias VLANs etiquetadas (entre switches). |
| **Native VLAN** | VLAN sin etiquetar en un trunk (por defecto VLAN 1). |
| **Inter-VLAN routing** | Router/L3 switch para comunicar VLANs. |

# Ataques

| Ataque | Idea |
|--------|------|
| **VLAN Hopping (switch spoofing)** | Hacerse pasar por switch (DTP) para recibir todas las VLANs. |
| **VLAN Hopping (double tagging)** | Doble etiqueta 802.1Q → saltar a otra VLAN abusando de la native VLAN. |

# Mitigación

Desactivar DTP (puertos en modo access explícito), cambiar la native VLAN y no usar VLAN 1, deshabilitar puertos sin uso, ACLs entre VLANs.

# Recursos
### [[DMZ]]
### [Wikipedia — VLAN](https://en.wikipedia.org/wiki/VLAN) · [HackTricks — VLAN hopping](https://book.hacktricks.xyz/generic-methodologies-and-resources/pentesting-network)
