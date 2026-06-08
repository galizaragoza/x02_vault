#reference

**Arquitectura de software** = decisiones estructurales de alto nivel sobre un sistema: cómo se divide en componentes, cómo se comunican y qué propiedades (escalabilidad, mantenibilidad, seguridad) prioriza. Son las decisiones **caras de cambiar** después.

# Estilos arquitectónicos

| Estilo | Idea | Cuándo |
|--------|------|--------|
| **Monolito** | Todo en un solo despliegue. | Apps pequeñas/medianas, equipos chicos, inicio. |
| **Microservicios** | Servicios independientes por dominio. | Escala, equipos grandes, despliegue independiente. |
| **Cliente-servidor** | Cliente consume un servidor central. | Web/apps clásicas. |
| **Event-driven** | Componentes reaccionan a eventos (colas/streams). | Asíncrono, desacoplado, alto volumen. |
| **Serverless / FaaS** | Funciones efímeras gestionadas por el cloud. | Cargas variables, sin gestionar infra. |
| **Capas (layered)** | Presentación / lógica / datos. | Separación de responsabilidades. |
| **Hexagonal / Ports & Adapters** | Núcleo aislado de detalles externos. | Testabilidad, independencia de framework. |

# Atributos de calidad

Escalabilidad, disponibilidad, rendimiento, mantenibilidad, seguridad, observabilidad, coste. La arquitectura es siempre un **compromiso** entre ellos (no se maximizan todos a la vez).

# Principios

- **Separación de responsabilidades** (SoC) y bajo **acoplamiento** / alta **cohesión**.
- **DRY**, **KISS**, **YAGNI**.
- **SOLID** (a nivel de diseño OO).
- **12-Factor App** para servicios cloud-native.
- **Defense in depth** y *secure by design* (la seguridad es transversal).

# Patrones comunes

API Gateway, Load Balancer, Cache (Redis), Message Queue (Kafka/RabbitMQ), CQRS, Circuit Breaker, Saga, Sidecar.

# Recursos
### [[software-architecture]]
### [System Design Primer](https://github.com/donnemartin/system-design-primer) · [The Twelve-Factor App](https://12factor.net/)
