# VPN y TOR
Todos los proveedores de VPN mantienen algún tipo de logs, lo mejor a lo que se puede aspirar es una VPN que no venda datos, por ende todo servicio gratuito está totalmente fuera.
Algunas buenas opciones son:
- [AirVPN](https://airvpn.org/)
- [ProtonVPN](https://protonvpn.com/)
- [MullvadVPN](https://mullvad.net/en)
Evidentemente, solo se debe pagar en cripto, o de ser posible con tarjetas de prepago compradas en efectivo.

En lo que respecta a TOR, hay que ser consciente de que pese a sus virtudes, el primer nodo va a saber igualmente tu IP, y según como de meticuloso sea la persona o grupo adversario, TOR no basta para ser anónimo

# Ubicación
Para aumentar la anonimidad es crucial ser cuidadoso con la ubicación física. Si dependemos solo de TOR+VPN estamos delegando muchísima parte de la "responsabilidad de anonimidad" en los intermediarios que en ambos casos son imprescindibles para emplear dichas tecnologías.
Para ser realmente anónimo, hay que ser tan cuidadoso con el rastro físico como con la huella digital.
Rotación de ubicaciones: Busca lugares donde haya wi-fi público gratuito o con contraseñas débiles, para identificar estos lugares, puedes servirte del proyecto [wifimap](https://www.wifimap.io/) o conducir por la ciudad con un recibidor WiFi + antena direccional. No desarrolles patrones sencillos ni repitas siempre el mismo spot.
Otra opción es comprar un router portátil y hacer todos los gastos necesarios (planes de datos, SIM prepago...) con efectivo o preferiblemente cripto.

# Hardware
Portátil específico dedicado a las operaciones, OS efímero como [Tails](https://tails.net/) (borrado en cada reboot), preferiblemente booteado desde USB.
Este equipo debe contener únicamente información volátil y temporal, si es estrictamente necesario almacenar datos: Criptografía potente en almacenamiento portátil (SD, microSD, USB...).

# Bouncing servers
El portátil no va a ser nunca el actor de ninguna acción, lo usaremos para conectarnos mediante SSH a un bouncing server: un servidor contratado en la nube (al que solo se accede de manera segura) y en el que ya están operando varias capas de seguridad fuertes. Aquí podremos almacenar el loot e interactuar con las VMs más maliciosas.

Al bouncing server se accede después de asegurar la conexión TOR+VPN mediante un túnel SSH, todo desde unas condiciones descritas previamente ([[Anonymizing#Ubicación]] y [[Anonymizing#Hardware]]). Aquí se puede setupear un entorno al gusto y preparar nuestro toolset.
Evidentemente, el pago de este servicio debe ser totalmente anónimo y en cripto.
- [RamNode](https://ramnode.com/)
	- Solo BitCoin
- [NiceVPS](https://nicevps.net/)
	- Monero y Zcash
- [Cinfu](https://www.cinfu.com/)
	- Monero y Zcash
- [PiVPS](https://robot-trade.fr/en/tool/pivps/)
	- Monero y Zcash
- [SecureDragon](https://securedragon.net/)
	- Solo BitCoin

Estos servidores jamás van a interactuar con el objetivo, de manera que podemos darnos el lujo de mantenerlos durante periodos de tiempo más largos antes de cambiar (semanas o meses). Igualmente, cuanto más a menudo se borren y vuelvan a crear, más seguro es nuestro sistema.

[How to setup zsh](https://www.howtoforge.com/tutorial/how-to-setup-zsh-and-oh-my-zsh-on-linux/)
[WPA/WPA2 Wifi attacking](https://youtu.be/yvNKuZqRmJ4)

# La infaestructura de ataque
La infraestructura de ataque es mucho más volátil y solo debe conservarse un par de días. Se debe rotar por cada operación y/u objetivo, si es posible, para evitar que se pueda atar la misma IP a distintos ataques en distintos targets.
[[Attck_Inf]]
