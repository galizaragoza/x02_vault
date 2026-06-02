#reference #Software

**cgroups** (control groups) = funcionalidad del kernel Linux para **limitar, priorizar y contabilizar** el uso de recursos (CPU, memoria, I/O, red) de grupos de procesos. Junto con **namespaces**, son la base de la contenerización (Docker, LXC, systemd).

![[cgroups.png]]

# Para qué

| Controlador | Limita |
|-------------|--------|
| **cpu** | Reparto/cuota de CPU. |
| **memory** | RAM máxima (OOM si se supera). |
| **io** (blkio) | Ancho de banda de disco. |
| **pids** | Número de procesos. |
| **devices** | Acceso a dispositivos. |

> **cgroups** = recursos. **namespaces** = aislamiento de la *vista* (PID, red, montajes, usuarios). Contenedor ≈ namespaces + cgroups + filesystem.

# v1 vs v2

| | cgroups v1 | cgroups v2 |
|--|-----------|------------|
| Jerarquía | Múltiple (una por controlador) | Única, unificada |
| Estado | Legacy | Estándar actual |
| Montaje | `/sys/fs/cgroup/<controlador>/` | `/sys/fs/cgroup/` |

# Uso

```bash
# Ver jerarquía y controladores
cat /sys/fs/cgroup/cgroup.controllers
systemd-cgls                       # árbol de cgroups
systemd-cgtop                      # uso de recursos en vivo

# Limitar un servicio con systemd
systemctl set-property myservice.service MemoryMax=512M CPUQuota=50%

# Crear cgroup v2 manual y limitar memoria
mkdir /sys/fs/cgroup/mygrp
echo 268435456 > /sys/fs/cgroup/mygrp/memory.max   # 256MB
echo $$ > /sys/fs/cgroup/mygrp/cgroup.procs        # mover shell actual
```

# Relevancia en seguridad

- Aislamiento/limitación de contenedores; un mal config permite **escape** o DoS por agotamiento.
- El grupo `docker`/contenedores privilegiados pueden abusar de cgroups (ver [[Groups]] y release_agent escapes).

# Recursos
### [[Capabilities]] · [[Groups]]
### [man cgroups(7)](https://man7.org/linux/man-pages/man7/cgroups.7.html)
