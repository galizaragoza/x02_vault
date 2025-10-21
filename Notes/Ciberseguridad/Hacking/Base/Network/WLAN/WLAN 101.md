# Que es una WLAN
WLAN significa Wireless Local Area Network, y es básicamente una red que ha sustituido el cableado físico por redes inalámbricas parcial o totalmente.

## Frame 801.11
![[801-11-frame.png|693x385]]

- **Frame Control -** This identifies the type of wireless frame and contains subfields for protocol version, frame type, address type, power management, and security settings.
- **Duration -** This is typically used to indicate the remaining duration needed to receive the next frame transmission.
- **Address1 -** This usually contains the MAC address of the receiving wireless device or AP.
- **Address2 -** This usually contains the MAC address of the transmitting wireless device or AP.
- **Address3 -** This sometimes contains the MAC address of the destination, such as the router interface (default gateway) to which the AP is attached.
- **Sequence Control -** This contains information to control sequencing and fragmented frames.
- **Address4 -** This usually missing because it is used only in ad hoc mode.
- **Payload -** This contains the data for transmission.
- **FCS -** This is used for Layer 2 error control.

## Evolución de los métodos de seguridad de WLANs
### WEP
Legacy, totalmente obsoleto
### WPA
También obsoleto, usa el protocolo de encriptado TKIP (Temporal Key Integrity Protocol), aún está presente como opción mixta en algunos routers por razones de compatibilidad con dispositivos viejos.
### WPA2
Cayendo en desuso pero aún compatible en la mayoría de APs debido a dispositivos que no soportan WPA3, usa el algoritmo [[!AES]], que es un estándar de cifrado simétrico.
### WPA3
Incorpora [[!SAE]] (Simultaneous Authentication of Equals), reforzando contra ataques de diccionario y fuerza bruta respecto al handshake PSK de WPA2, mejora los métodos de encriptado, cifra por dispositivo, protege contra [[!KRACK]]

![[WPA1-3.png]]

## Métodos de autenticación
### Shared Key
La mediante la cual se encripta es la misma para todos los dispositivos que pertenecen a la red, requiere rotación para mayor seguridad y es sobre todo para redes domésticas.
### Radius
Las llaves son distintas para cada dispositivo que se conecta a la red, el servidor RADIUS puede ser on-prem o cloud-based, en cualquier caso el WAP consulta al servidor para autenticar al usuario.
Supone una seguridad mucho más robusta, aunque puede ser tedioso de configurar y provocar nuevas vulnerabilidades.
![[RADIUS.png]]

### Mutual auth

![[mutual-auth.png]]

## Fuentes
[Explicación video beamforming](https://youtu.be/UoVxAKkPys0)
[WIFI 7](https://youtu.be/gVBEcvMHKr0?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
[Wifi 7 Wi-fi oficial pdf](https://www.wi-fi.org/system/files/Wi-Fi_CERTIFIED_7_Highlights_202401_0.pdf)
[Hacking WPA](https://youtu.be/44I1wfgGT80?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
[WPA3 Dragonfly Handshake vuln](https://wpa3.mathyvanhoef.com/)