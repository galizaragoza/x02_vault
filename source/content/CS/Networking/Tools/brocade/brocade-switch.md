Gestión del **switch lógico y del chassis** físico en Fabric OS: estado e identidad del switch, dirección IP de gestión, fecha/hora y NTP, monitorización ambiental (temperatura, ventiladores, fuentes), alta disponibilidad en directores, licencias y *Virtual Fabrics*. Es el punto de partida para administrar un equipo Brocade. Véase el índice en [[brocade-fos]].

---

# Estado e identidad del switch

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `switchShow` | Estado del switch y tabla de puertos (índice, tipo, estado, WWN conectado). | `switchShow` |
| `switchName` | Muestra o fija el nombre simbólico del switch. | `switchName "san-edge-01"` |
| `switchEnable` | Pone el switch online (habilita el dominio en la fabric). | `switchEnable` |
| `switchDisable` | Pone el switch offline (lo saca de la fabric; necesario para muchos cambios). | `switchDisable` |
| `switchStatusShow` | Resumen de salud global (OK/Marginal/Down) del switch. | `switchStatusShow` |
| `switchStatusPolicyShow` | Umbrales que determinan el estado de salud. | `switchStatusPolicyShow` |
| `fastBoot` | Reinicia el switch omitiendo el POST. | `fastBoot` |
| `reboot` | Reinicio completo (con POST). | `reboot` |
| `uptime` | Tiempo en servicio y carga del sistema. | `uptime` |

---

# Chassis y entorno físico

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `chassisShow` | Inventario de FRUs y datos de fabricación del chassis. | `chassisShow` |
| `chassisName` | Muestra o fija el nombre del chassis. | `chassisName "DCX-A"` |
| `chassisEnable` / `chassisDisable` | Habilita/deshabilita todos los switches lógicos del chassis. | `chassisDisable` |
| `slotShow` | Estado de las blades/slots (en directores). | `slotShow` |
| `tempShow` | Lecturas de los sensores de temperatura. | `tempShow` |
| `fanShow` | Estado y RPM de los ventiladores. | `fanShow` |
| `psShow` | Estado de las fuentes de alimentación. | `psShow` |
| `sensorShow` | Lectura combinada de todos los sensores. | `sensorShow` |
| `sysShutdown` | Apaga el sistema de forma ordenada. | `sysShutdown` |
| `historyShow` | Historial de inserción/extracción de FRUs. | `historyShow` |

---

# Gestión IP, fecha y NTP

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `ipAddrShow` | Muestra la configuración IP de gestión (CP/switch). | `ipAddrShow` |
| `ipAddrSet` | Configura IP, máscara y gateway de gestión. | `ipAddrSet` |
| `dnsConfig` | Configura servidores DNS y dominio. | `dnsConfig` |
| `date` | Muestra o ajusta la fecha/hora local del switch. | `date "0625120026"` |
| `tsClockServer` | Define el/los servidor(es) NTP de la fabric. | `tsClockServer "10.0.0.1"` |
| `tsTimeZone` | Configura la zona horaria. | `tsTimeZone --interactive` |
| `ipFilter --show` | Muestra el filtro IP de acceso a la gestión (ver [[brocade-security]]). | `ipFilter --show` |

> El reloj del switch puede disciplinarse por NTP igual que un host; comprobar la fuente con `tsClockServer` y, del lado del servidor, con [[ntpq]].

---

# Configuración de parámetros del switch

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `configure` | Asistente interactivo de parámetros de fabric (Domain ID, formato PID, etc.). Requiere `switchDisable`. | `configure` |
| `configShow` | Vuelca los parámetros de configuración actuales. | `configShow "fabric.ops"` |
| `fosConfig --show` | Muestra el estado de funcionalidades FOS (VF, FCR…). | `fosConfig --show` |
| `fosConfig --enable vf` | Habilita Virtual Fabrics. | `fosConfig --enable vf` |
| `bannerSet` | Define el banner de login. | `bannerSet "Acceso restringido"` |
| `motd`/`bannerShow` | Muestra el banner configurado. | `bannerShow` |

---

# Alta disponibilidad (directores)

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `haShow` | Estado de los dos procesadores de control (CP activo/standby). | `haShow` |
| `haFailover` | Conmuta el CP activo al de reserva. | `haFailover` |
| `haSyncShow` | Comprueba la sincronización entre CPs. | `haSyncShow` |
| `haDisable` / `haEnable` | Deshabilita/habilita la alta disponibilidad. | `haEnable` |
| `haReboot` | Reinicia el CP standby. | `haReboot` |

---

# Virtual Fabrics (VF)

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `setContext` | Cambia al contexto de un switch lógico (Fabric ID). | `setContext 10` |
| `lsCfg --show -all` | Muestra la configuración de switches lógicos. | `lsCfg --show -all` |
| `lsCfg --create` | Crea un switch lógico con un Fabric ID. | `lsCfg --create 20` |
| `lscfg --config` | Asigna puertos a un switch lógico. | `lscfg --config 20 -slot 3 -port 0-15` |
| `fcrConfigure` | Configuración del backbone para enrutado entre fabrics (ver [[brocade-fabric]]). | `fcrConfigure` |

---

# Licencias

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `licenseShow` | Lista las licencias instaladas y las funciones que habilitan. | `licenseShow` |
| `licenseAdd` | Instala una clave de licencia. | `licenseAdd "ABCD1234..."` |
| `licenseRemove` | Elimina una licencia. | `licenseRemove "ABCD1234..."` |
| `licenseIdShow` | Muestra el License ID (WWN) necesario para generar licencias. | `licenseIdShow` |
| `licensePort --show` | Estado del licenciamiento *Ports on Demand* (POD). | `licensePort --show` |
