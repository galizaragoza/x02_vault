### DNS Reflection
En un ataque de DNS Reflection, el atacante envía queries DNS a servidores públicamente accesibles, pero spoofeando la dirección IP fuente para que parezca que la query es enviada desde la IP de la víctima, de modo que cuando el servidor envía su respuesta, la entrega a la víctima y no al atacante.
### DNS Amplification
Un ataque de amplificación DNS es un paso extra respecto a los ataques DNS Reflection, el atacante envía queries pequeñas que provocan respuestas más grandes, aprovechándose del diseño de DNS, que permite la entrada de queries pequeñas y salida de queries mucho más grandes. Spoofeando la IP, el atacante puede llegar a amplificar 30 o 50 veces el tráfico enviado a la víctima. 
Combinar amplificación y reflección puede generar enormes cantidades de tráfico en el lado de la víctima usando recursos mínimos.

![[DNS-Amplification-Attack-1.png|634x332]]

https://www.indusface.com/learning/dns-reflection-attack-amplification-attack/