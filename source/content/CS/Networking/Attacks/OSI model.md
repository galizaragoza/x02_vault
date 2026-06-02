#reference #Networking

**Modelo OSI** = marco conceptual de 7 capas que estandariza cómo se comunican los sistemas en red. Cada capa da servicio a la superior y se apoya en la inferior. Útil para razonar dónde ocurre un problema o un ataque.

![[osi-model.png|893x487]]

# Las 7 capas

| # | Capa | Función | PDU | Ejemplos | Ataques típicos |
|---|------|---------|-----|----------|-----------------|
| 7 | **Aplicación** | Interfaz con el usuario/app | Datos | HTTP, DNS, SMTP | Inyección web, phishing |
| 6 | **Presentación** | Formato, cifrado, codificación | Datos | TLS, JPEG, ASCII | Ataques SSL/TLS |
| 5 | **Sesión** | Establece/gestiona sesiones | Datos | RPC, NetBIOS | Session hijacking |
| 4 | **Transporte** | Entrega extremo a extremo, fiabilidad | Segmento | TCP, UDP | SYN flood, port scan |
| 3 | **Red** | Direccionamiento lógico y routing | Paquete | IP, ICMP | [[!IP spoofing]], routing attacks |
| 2 | **Enlace** | Direccionamiento físico (MAC), tramas | Trama | Ethernet, ARP, switch | [[ARP Cache Poisoning]], [[MAC Spoofing]] |
| 1 | **Física** | Bits sobre el medio | Bit | Cables, RF, hub | Wiretapping, jamming |

> Mnemotecnia (arriba→abajo): **A**plicación **P**resentación **S**esión **T**ransporte **R**ed **E**nlace **F**ísica.

# OSI vs TCP/IP

| OSI (7) | TCP/IP (4) |
|---------|------------|
| Aplicación + Presentación + Sesión | **Aplicación** |
| Transporte | **Transporte** |
| Red | **Internet** |
| Enlace + Física | **Acceso a red** |

> El modelo **TCP/IP** es el que se usa en la práctica; OSI es el de referencia pedagógica.

# Recursos
### [[IP_CS]] · [[DNS 101]]
### [What is OSI Model — ByteByteGo](https://youtu.be/0y6FtKsg6J4)
