# Troubleshooting (Linux)

#Linux #Troubleshooting #Monitoring

Playbooks de refresco rápido para diagnosticar el estado de un host Linux por recurso. Cada guía recopila conceptos, métricas clave y comandos (con variedad de herramientas) ordenados por flujo de diagnóstico, no por herramienta.

| Guía | Cubre | Cuándo |
|------|-------|--------|
| [[RAM-Swap]] | RAM, caché, swap, presión de memoria, OOM killer | "se queda sin memoria", lentitud por swap |
| [[CPU]] | Carga, %us/sy/wa/st, por core/hilo, frecuencia, profiling | "la CPU está al 100%", load alto |
| [[Discos-Filesystems]] | Espacio, **inodos**, uso, latencia E/S, SMART, fsck | "disco lleno", "disco lento", errores de FS |
| [[Red-Conectividad]] | Interfaces, ruta, **DNS (diagnóstico + caché/DoT)**, sockets, puertos, captura, firewall, **mantenimiento** (DHCP, MTU) | "no hay red", "no conecta al puerto X", "no resuelve DNS" |

> Regla común: aislar la capa antes de actuar. RAM ≠ caché, load ≠ CPU, "disco lleno" puede ser inodos, y un fallo de red se depura subiendo por la pila (enlace → IP → DNS → puerto → app).
