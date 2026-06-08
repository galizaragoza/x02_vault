# Que es DHCP Spoofing
Un ataque de Spoofing DHCP se da cuando un servidor DHCP malicioso se conecta a la red y proporciona a los equipos configuraciones IP falsas, tales como:
1. **Default gateway maliciosa**
2. **DNS Server malicioso**
3. **IP adress maliciosa**

![[DHCP spoofing.png]]

# DORA (contexto)

DHCP asigna config IP en 4 mensajes: **D**iscover → **O**ffer → **R**equest → **A**ck. El cliente acepta la **primera oferta** que llega → si el atacante responde antes que el servidor legítimo, gana.

# Variantes

| Ataque | Idea |
|--------|------|
| **Rogue DHCP server** | Servidor falso que reparte gateway/DNS maliciosos → MITM. |
| **DHCP Starvation** | Agotar el pool del servidor legítimo con miles de DISCOVER (MACs falsas) → solo responde el rogue. |

```bash
# Starvation
yersinia dhcp -attack 1        # o dhcpstarv
# Rogue server (ej. con dnsmasq / Ettercap)
ettercap -T -M dhcp:/255.255.255.0/192.168.1.1
```

Combinado con [[MAC Spoofing]] para generar muchos clientes falsos.

# Impacto

Default gateway malicioso → todo el tráfico pasa por el atacante (MITM). DNS malicioso → redirigir dominios. Útil para sniffing, SSL stripping, phishing.

# Mitigación

- **DHCP Snooping** (switch): solo puertos *trusted* pueden enviar OFFER/ACK.
- Limitar tasa de DHCP por puerto (anti-starvation).
- Port security + 802.1X.

# Recursos
### [[ARP Cache Poisoning]] · [[MAC Spoofing]]
### [HackTricks — DHCP attacks](https://book.hacktricks.xyz/generic-methodologies-and-resources/pentesting-network)