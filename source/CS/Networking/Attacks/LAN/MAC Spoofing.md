#reference #Networking

**MAC Spoofing** = cambiar la dirección MAC de una interfaz de red para suplantar a otro dispositivo. Sirve para evadir filtros por MAC, saltarse portales cautivos/NAC, o como base de ataques LAN ([[ARP Cache Poisoning]], [[DHCP Spoofing]]).

# Para qué se usa

| Objetivo | Cómo ayuda el spoofing |
|----------|------------------------|
| Bypass de MAC filtering | Clonar la MAC de un equipo ya autorizado. |
| Saltar portal cautivo / NAC | Suplantar un dispositivo ya autenticado (impresora, IoT). |
| Evadir cuotas/time limits | Aparentar ser un cliente nuevo. |
| Anonimato / anti-tracking | Ocultar la MAC real (que identifica el hardware). |
| Habilitar otros ataques | Suplantar el gateway en ataques MITM. |

# Cómo

```bash
# Manual (ip)
sudo ip link set dev eth0 down
sudo ip link set dev eth0 address 00:11:22:33:44:55
sudo ip link set dev eth0 up

# macchanger
sudo macchanger -r eth0          # MAC aleatoria
sudo macchanger -m AA:BB:CC:DD:EE:FF eth0   # MAC concreta
sudo macchanger -p eth0          # restaurar la original (permanente/HW)
sudo macchanger -s eth0          # ver MAC actual y original

# Windows (PowerShell / registro o propiedades del adaptador)
Set-NetAdapter -Name "Wi-Fi" -MacAddress "001122334455"
```

> El primer octeto debe tener el bit unicast (par) y conviene respetar OUIs reales para no levantar sospechas. MAC aleatoria por privacidad es habitual en móviles modernos.

# Detección / mitigación

- **802.1X / NAC** (autenticación por certificado, no por MAC) — el control real.
- **Port security** (Cisco): limitar MACs por puerto, sticky MAC.
- **DHCP snooping** + Dynamic ARP Inspection.
- Monitorización de MACs duplicadas en la red.

# Recursos
### [[ARP Cache Poisoning]] · [[DHCP Spoofing]]
### [macchanger](https://github.com/alobbs/macchanger)
