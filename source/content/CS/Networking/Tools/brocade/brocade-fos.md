**Fabric OS (FOS)** es el sistema operativo de los switches y directores Fibre Channel de **Brocade** (hoy Broadcom). Su CLI —accesible por SSH/serie y autenticada por roles (RBAC)— gestiona toda la SAN: estado del chassis, puertos, fabric, enrutado, zoning, diagnóstico, firmware y seguridad. Los comandos son *camelCase* (`switchShow`, `portCfgSpeed`) y muchos cambios no son persistentes hasta ejecutar la acción de guardado correspondiente (`cfgSave` en zoning, `configUpload` para respaldo). Esta nota es el índice de la sección; cada categoría tiene su propia guía.

```
switch:admin> <comando> [argumentos]
```

> **Convenciones FOS:** los puertos se identifican por índice (`0`–`N`) o, en directores, por `slot/port` (`3/12`). El estado base se lee con `switchShow`; los dispositivos conectados, con `nsShow`. Para entornos con *Virtual Fabrics* (VF) el contexto se cambia con `setContext`. Casi todo comando `*Show` es de solo lectura; los `*Cfg*`, `*Set`, `*Enable/Disable` modifican estado.

---

# Mapa de la sección

| Guía | Contenido |
|------|-----------|
| [[brocade-switch]] | Switch y chassis: estado, nombre, IP de gestión, fecha/NTP, entorno (temp/fan/PS), HA, licencias, VF. |
| [[brocade-ports]] | Puertos: estado, habilitación, velocidad, tipo (E/F/EX), long distance, trunk, QoS, estadísticas, SFP. |
| [[brocade-fabric]] | Fabric y enrutado: switches, principal, ISL/trunk, FSPF, DLS, FC Routing (FCR), Access Gateway, fcPing. |
| [[brocade-zoning]] | Zoning: alias, zonas, cfg, peer zones, `defZone`, flujo `cfgSave`/`cfgEnable`. |
| [[brocade-diagnostics]] | Diagnóstico y monitorización: `supportSave`, error/audit log, MAPS, Flow Vision, SNMP, syslog. |
| [[brocade-firmware]] | Firmware y configuración: `firmwareDownload`, `configUpload`/`Download`, reboot, licencias. |
| [[brocade-security]] | Seguridad: usuarios y roles, AAA (RADIUS/LDAP/TACACS+), ipFilter, políticas ACL, DH-CHAP, certificados, FIPS. |

---

# Comandos de orientación rápida

Lecturas mínimas para situarse en un switch desconocido.

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `version` | Versión de Fabric OS y del kernel. | `version` |
| `switchShow` | Estado del switch y de todos sus puertos. | `switchShow` |
| `fabricShow` | Switches presentes en la fabric (domain, WWN, nombre). | `fabricShow` |
| `nsShow` | Dispositivos (N_Ports) registrados localmente en el name server. | `nsShow` |
| `chassisShow` | Inventario de FRUs del chassis (blades, fuentes, ventiladores). | `chassisShow` |
| `cfgActvShow` | Configuración de zoning efectiva (activa). | `cfgActvShow` |
| `uptime` | Tiempo en servicio y carga. | `uptime` |
| `help [cmd]` | Ayuda general o de un comando concreto. | `help portcfgspeed` |
