Session Initiation Protocol es un protocolo utilizado para conexiones ==VoIP==, y es responsable de inicializar, modificar y terminar las sesiones entre 2 o más participantes.
Los mensajes SIP describen la identidad de los participantes de una llamada y como llegar a través de IP. Dentro de un mensaje SIP puede haber un mensaje SDP (Session Description Protocol), que define el tipo de canales de medios que se usarán en la sesión.

![[sip-server.png]]

Básicamente, sienta las bases de la llamada, identifica a los participantes y las rutas para llegar a ellos y otros ajustes importantes para la sesión, la transimisión del contenido de la llamada en sí se hace mediante [[!RTP]](Real time Transport Protocol).

## Cómo funciona
Los mensajes del protocolo SIP se tramitan por TCP o UDP, similar a HTTP, la comunicación se basa en solicitudes y respuestas con sencillos códigos y texto.

![[SIP-session.png|271x227]]

En este ve claramente:
1. En primer lugar el teléfono que llama envia un `INVITE`
2. El receptor envía un código `100 - Trying` de vuelta
3. Cuando el receptor comienza a sonar, envía un mensaje de nuevo: `180 - Ringing`
4. Una vez que el receptor ha aceptado la llamada, envía al teléfono que está llamando un estado `200 OK`
5. El teléfono que estaba llamando responde con un acknowledgement `ACK`
6. Se inicia la sesión y empieza a transmitirse el contenido de la conversación a través de RTP
7. El receptor cuelga, enviando un estado `BYE`
8. El teléfono que inició la llamada responde con `200 - OK` y se termina la sesión

## Fuentes
[Artículo VOIP Studio sobre SIP](https://voipstudio.com/es/blog/voip-y-sip-en-que-se-diferencian/#toc_Que_es_SIP)
[# ¿Qué es y cómo Funciona SIP?](https://www.3cx.es/voip-sip/sip/)
## Herramientas
[sipvicious](https://github.com/EnableSecurity/sipvicious)
	SIPVicious OSS is a VoIP security testing toolset. This toolset is useful in simulating VoIP hacking attacks against PBX systems especially through identification, scanning, extension enumeration and password cracking.

