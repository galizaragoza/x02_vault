#reference #Networking

**KRACK** (Key Reinstallation Attack, 2017) = ataque al **4-way handshake de WPA2**. Forzando la **reinstalación de una clave ya en uso**, se resetea el nonce/contador de paquetes, lo que permite **descifrar, reproducir y a veces inyectar** tráfico Wi-Fi. Afecta al protocolo, no a una implementación concreta.

# El 4-way handshake (contexto)

WPA2 negocia la **PTK** (clave de sesión) en 4 mensajes entre cliente (supplicant) y AP (authenticator). La clave se "instala" tras el mensaje 3.

# El fallo

El mensaje 3 puede retransmitirse si el AP no recibe confirmación. Cada vez que el cliente lo recibe, **reinstala la misma PTK** y reinicia el **nonce de transmisión** y el replay counter.

```
Atacante (MITM) bloquea el msg4 -> AP reenvía msg3
Cliente reinstala PTK -> nonce vuelve a su valor inicial
Reutilización de nonce -> keystream repetido -> descifrado
```

Reutilizar un nonce con el mismo keystream rompe la confidencialidad del cifrado de flujo. Con **TKIP/GCMP** el impacto es peor (inyección/forja).

# Impacto y límites

- Permite **descifrar** paquetes; con HTTPS/VPN el contenido sigue cifrado en la capa superior.
- Requiere posición **MITM** en el alcance del Wi-Fi (no remoto).
- **No** revela la contraseña Wi-Fi (PSK).
- Clientes Linux/Android (wpa_supplicant) fueron especialmente vulnerables (reinstalaban clave todo-ceros).

# Mitigación

Parchear cliente **y** AP, WPA3 (SAE — ver [[SAE]]), forzar HTTPS/HSTS y VPN. La corrección impide reinstalar una clave ya usada.

# Recursos
### [[SAE]]
### [krackattacks.com](https://www.krackattacks.com/)
### [Paper original — Vanhoef & Piessens](https://papers.mathyvanhoef.com/ccs2017.pdf)
