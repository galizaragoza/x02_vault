#reference #Ciberseguridad

**Resiliencia** = capacidad de un sistema de **seguir operando** (o recuperarse rápido) ante fallos, ataques o desastres. Pilar de la **disponibilidad** (la "A" de la tríada CIA).

# Alta disponibilidad

Minimizar el tiempo de inactividad. Se mide en **"nueves"**:

| Nivel | Disponibilidad | Downtime/año |
|-------|----------------|--------------|
| 2 nueves | 99% | ~3,65 días |
| 3 nueves | 99,9% | ~8,8 horas |
| 4 nueves | 99,99% | ~52 min |
| **5 nueves** | 99,999% | ~5,3 min |

# Punto único de fallo (SPOF) y redundancia

Un **SPOF** es un componente cuyo fallo tumba todo el sistema. Se elimina con redundancia:

| Técnica | Qué redunda |
|---------|-------------|
| **N+1** | Un componente extra sobre los necesarios. |
| **RAID** | Redundancia de discos (1, 5, 6, 10…). |
| **Spanning Tree (STP)** | Evita bucles y da rutas L2 alternativas. |
| **Router redundancy** | VRRP/HSRP: gateway redundante. |
| **Location redundancy** | Múltiples sites/datacenters (geo-redundancia). |
| **Corriente** | UPS + generadores (SAI). |
| **HVAC** | Refrigeración redundante. |
| **Resilient design** | Diseño tolerante a fallos de extremo a extremo. |

# Continuidad y recuperación

| Métrica | Significado |
|---------|-------------|
| **RTO** (Recovery Time Objective) | Tiempo máximo tolerable para restaurar. |
| **RPO** (Recovery Point Objective) | Pérdida de datos máxima tolerable (cuánto hacia atrás). |
| **BCP / DRP** | Plan de continuidad de negocio / recuperación ante desastres. |
| **Backups** | Regla 3-2-1: 3 copias, 2 medios, 1 fuera del sitio. |

# Recursos
### [[Hardening]] · [[triada-cia]]
### [Wikipedia — High availability](https://en.wikipedia.org/wiki/High_availability)
