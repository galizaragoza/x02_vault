#reference #Ciberseguridad #Networking

**DMZ** (Demilitarized Zone) = subred intermedia que expone servicios públicos (web, mail, DNS) a Internet **aislándolos** de la red interna (LAN). Si se compromete un servidor de la DMZ, el atacante **no** llega directo a la red interna.

![[dmz.png]]

# Diseño

```
Internet ──[Firewall ext]── DMZ (web, mail, DNS) ──[Firewall int]── LAN interna
```

| Topología | Idea |
|-----------|------|
| **Single firewall (3 patas)** | Un firewall con 3 interfaces: WAN, DMZ, LAN. |
| **Dual firewall** | Dos firewalls (perímetro + interno); más seguro, defensa en profundidad. |

# Reglas típicas

- Internet → DMZ: permitido solo a los puertos de servicio (80/443…).
- DMZ → LAN: **denegado** por defecto (o muy restringido).
- LAN → DMZ/Internet: permitido según política.

> Principio: la DMZ es "tierra de nadie". Un servicio expuesto comprometido queda contenido y no pivota a la LAN si las reglas son correctas.

# Relación

Segmentación de red ([[VLAns]]) para aislar zonas; un atacante en la DMZ intentará **pivoting** hacia la interna si el firewall interno es débil.

# Recursos
### [[VLAns]]
### [Wikipedia — DMZ (computing)](https://en.wikipedia.org/wiki/DMZ_(computing))
