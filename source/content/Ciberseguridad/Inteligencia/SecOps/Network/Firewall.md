Un firewall es un dispositivo/software perimetral que aplica una serie de normas de filtrado del tráfico. En base a las reglas establecidas por el admin, el firewall deniega o permite tráfico.
## Tipos de Firewall
### Stateless
Suelen formar parte del firewall del router y operan en base a data de las capas 3 y 4 del modelo OSI. Filtran el tráfico en base criterios específicos leídos de una tabla.
- **Ventajas**:
	- Simplicidad, alta compatibilidad e instalación sencilla
	- Impacto bajo en los recursos de red
	- Buena defensa inicial sólida
- **Desventajas**:
	- Susceptibles a [[IP spoofing]]
	- Poco fiables con paquetes fragmentados
	- Los ACLs pueden ser tediosos de implementar y mantener
	- Estáticos, no permiten filtrado dinámico/flexible o context-based
### Stateful 
Opera en las capas 3 a 5, y mantiene una tabla con info de las conexiones para un análisis más profundo.
- **Ventajas**:
	- Control más riguroso
	- Mejoran el rendimiento de los packet filters o los servers proxy
	- Protección contra spoofing y DoS
	- Mas logs
- **Desventajas**:
	- No protegen contra ataques en la capa 7
	- No todos los protocolos generan tablas
	- No cubre conexiones que negocian en puerto dinámico
	- No soporta autenticación de usuario
### Application Gateway (Proxy firewall)
Opera en las capas 3,4,5 y 7 y la mayoría del control/filtrado se hace en software, un proxy realiza las conexiones en nombre del cliente, de manera que el servidor solo ve e interactúa con el proxy
### Next-gen
Firewall stateful con nuevas features de next-gen como:
- IPS integrado
- Detección, aviso y bloqueo de apps arriesgadas
### Host-based
100% software que corre en cada equipo de forma individual
### Híbrido
### Zone-based Policy Firewall(ZPF)
ZPF es una evolución de los firewalls que permite asignar las interfaces a zonas, que a su vez están asociadas a unas políticas de seguridad. Esto rompe con el modelo anterior que limita la configuración de seguridad a la interfaz del router. Esto trae una serie de ventajas respecto a los firewalls clásicos.
- No depende de ACLs
- El router bloquea por defecto salvo permisión explícita
- Las políticas son sencillas de leer y diagnosticar
- Las interfaces virtuales y físicas pueden agruparse por zonas
- Las políticas se aplican al tráfico unidireccional entre zonas
Sin embargo, implementar ZPF requiere ciertos pasos:
1. Determinar las zonas
2. Establecer las políticas
3. Diseñar la infraestructura material
4. Identificar *subsets* en las zonas y adaptarse a las exigencias de tráfico