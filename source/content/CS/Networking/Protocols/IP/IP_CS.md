![[subnetting-cs.png]]
# Clases

| **Clase**   | **Rango del Primer Octeto** | **Máscara por Defecto** | **Propósito / Uso**                             | **Ejemplo de IP** |
| ----------- | --------------------------- | ----------------------- | ----------------------------------------------- | ----------------- |
| **Clase A** | `1` - `126`                 | `255.0.0.0`             | Redes masivas (Gubernamentales, ISPs).          | `10.0.0.1`        |
| **Clase B** | `128` - `191`               | `255.255.0.0`           | Redes medianas (Universidades, Empresas).       | `172.16.254.1`    |
| **Clase C** | `192` - `223`               | `255.255.255.0`         | Redes pequeñas (Hogar, Pequeñas oficinas).      | `192.168.1.50`    |
| **Clase D** | `224` - `239`               | N/A                     | **Multicast**: Transmisión de datos a un grupo. | `224.0.0.1`       |
| **Clase E** | `240` - `255`               | N/A                     | **Experimental**: Reservado para investigación. | `241.0.0.2`       |

# CIDR

| **CIDR** | **Máscara de Red (Decimal)** | **Total de IPs** | **IPs Útiles (Hosts)** | **Explicación Breve**                                     |
| -------- | ---------------------------- | ---------------- | ---------------------- | --------------------------------------------------------- |
| `/32`    | `255.255.255.255`            | 1                | 1                      | Define una **única IP** (Host específico).                |
| `/30`    | `255.255.255.252`            | 4                | 2                      | Ideal para enlaces **Punto a Punto** (Router a Router).   |
| `/29`    | `255.255.255.248`            | 8                | 6                      | Redes muy pequeñas (ej. servidores públicos limitados).   |
| `/27`    | `255.255.255.224`            | 32               | 30                     | Segmentación de departamentos pequeños.                   |
| `/24`    | `255.255.255.0`              | 256              | 254                    | El estándar para **LANs domésticas** y pequeñas oficinas. |
| `/21`    | `255.255.248.0`              | 2.048            | 2.046                  | Redes corporativas grandes con muchos dispositivos.       |
| `/16`    | `255.255.0.0`                | 65.536           | 65.534                 | Redes de campus o infraestructuras cloud extensas.        |
| `/8`     | `255.0.0.0`                  | 16.7M            | 16.7M                  | Redes globales o privadas masivas (ej. Red `10.x.x.x`).   |

2^ (32−CIDR) − 2 = Host disponibles

# IPs reservadas

| **Rango de Red**  | **Uso / Propósito**    | **Explicación Breve**                                                                                       |
| ----------------- | ---------------------- | ----------------------------------------------------------------------------------------------------------- |
| `0.0.0.0/8`       | **Red Local**          | Se utiliza para referirse a "esta red" o como dirección de origen antes de que un host tenga una IP (DHCP). |
| `10.0.0.0/8`      | **Privada (Clase A)**  | Reservada para redes internas privadas. Muy común en grandes empresas.                                      |
| `127.0.0.0/8`     | **Loopback**           | Direcciones de "retroalimentación". `127.0.0.1` apunta a la propia máquina local.                           |
| `169.254.0.0/16`  | **APIPA**              | Direcciones de enlace local autoasignadas cuando un servidor DHCP no responde.                              |
| `172.16.0.0/12`   | **Privada (Clase B)**  | Rango privado intermedio (de `172.16.x.x` a `172.31.x.x`).                                                  |
| `192.0.0.0/24`    | **Protocolos IETF**    | Reservada para asignaciones de protocolos de ingeniería de Internet.                                        |
| `192.168.0.0/16`  | **Privada (Clase C)**  | El estándar más usado en routers domésticos y pequeñas oficinas.                                            |
| `198.18.0.0/15`   | **Benchmark**          | Reservada para pruebas de rendimiento en dispositivos de red.                                               |
| `224.0.0.0/4`     | **Multicast**          | Antiguamente Clase D. Se usa para enviar datos a varios destinos simultáneamente.                           |
| `240.0.0.0/4`     | **Experimental**       | Antiguamente Clase E. Reservada para uso futuro o investigación.                                            |
| `255.255.255.255` | **Broadcast Limitado** | Se usa para enviar un paquete a todos los hosts de la red actual.                                           |