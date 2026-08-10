**Diagnóstico y monitorización** en Fabric OS: recogida de datos de soporte (`supportSave`/`supportShow`), registros de error y auditoría, el motor de políticas **MAPS** (Monitoring and Alerting Policy Suite), **Flow Vision** para análisis de tráfico, detección de cuellos de botella, y la integración con SNMP y syslog remoto. Es la base para troubleshooting y para enviar evidencias al fabricante. Véase el índice en [[brocade-fos]]; los contadores por puerto están en [[brocade-ports]].

---

# Recogida de datos de soporte

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `supportShow` | Vuelca en pantalla un dump completo de diagnóstico del switch. | `supportShow` |
| `supportSave` | Guarda los datos de soporte a un host remoto (FTP/SCP/SFTP). | `supportSave -n -h 10.0.0.9 -p scp -u admin -d /tmp` |
| `supportFtp` | Configura el destino por defecto para `supportSave`. | `supportFtp -s` |
| `diagShow` | Resumen de los resultados de diagnóstico del POST. | `diagShow` |
| `cliHistory` | Historial de comandos ejecutados (auditoría rápida). | `cliHistory` |

---

# Registros de error y auditoría

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `errShow` | Muestra el log de errores del sistema (RASlog) de forma paginada. | `errShow` |
| `errDump` | Vuelca el log de errores sin paginar (apto para `grep`). | `errDump` |
| `errClear` | Borra el log de errores. | `errClear` |
| `errModuleShow` | Lista los módulos que pueden generar mensajes RAS. | `errModuleShow` |
| `auditCfg` | Configura qué clases de eventos se auditan. | `auditCfg --class 1,2,4` |
| `auditDump` | Muestra el log de auditoría. | `auditDump -s` |
| `fabriclog --show` | Eventos de la fabric (merges, reconfiguraciones). | `fabriclog --show` |

---

# MAPS — Monitoring and Alerting Policy Suite

Motor de monitorización por políticas/reglas/umbral que reemplaza al antiguo Fabric Watch.

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `mapsConfig --show` | Estado y configuración global de MAPS. | `mapsConfig --show` |
| `mapsConfig --actions` | Define las acciones globales permitidas (raslog, snmp, email…). | `mapsConfig --actions raslog,snmp` |
| `mapsPolicy --show -summary` | Lista las políticas y cuál está activa. | `mapsPolicy --show -summary` |
| `mapsPolicy --enable` | Activa una política de monitorización. | `mapsPolicy --enable dflt_aggressive_policy` |
| `mapsRule --create` | Crea una regla (monitor, umbral, acción). | `mapsRule --create myCRC -monitor CRC -group ALL_PORTS -timebase min -op g -value 5 -action raslog` |
| `mapsRule --show` | Muestra las reglas definidas. | `mapsRule --show -all` |
| `mapsDb --show` | Estado de los recursos monitorizados (dashboard). | `mapsDb --show all` |
| `mapsSam --show` | Estadísticas de monitorización de recursos del sistema. | `mapsSam --show` |

---

# Flow Vision y rendimiento

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `flow --create` | Crea un flujo a monitorizar (por puerto/SID/DID/LUN). | `flow --create f1 -feature monitor -ingrport 12 -srcdev 0x010f00` |
| `flow --show` | Muestra estadísticas de los flujos definidos. | `flow --show f1` |
| `flow --delete` | Elimina un flujo. | `flow --delete f1` |
| `perfMonitorShow` | Estadísticas del monitor de rendimiento (legado). | `perfMonitorShow --class` |
| `bottleneckMon --show` | Estado de la detección de cuellos de botella. | `bottleneckMon --show -interval 60` |
| `bottleneckMon --enable` | Activa la detección de bottlenecks. | `bottleneckMon --enable -alert` |

---

# SNMP y syslog remoto

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `snmpConfig --show` | Muestra la configuración SNMP (comunidades, trap targets). | `snmpConfig --show mibCapability` |
| `snmpConfig --set` | Configura agente/usuarios/destinos SNMP (v1/v2c/v3). | `snmpConfig --set snmpv3` |
| `syslogdIpAdd` | Añade un servidor syslog remoto. | `syslogdIpAdd 10.0.0.20` |
| `syslogdIpShow` | Lista los servidores syslog configurados. | `syslogdIpShow` |
| `syslogdIpRemove` | Elimina un servidor syslog. | `syslogdIpRemove 10.0.0.20` |
| `syslogdFacility` | Fija la facility de syslog. | `syslogdFacility -l 4` |

> Para correlación con SNMP de red genérica, ver [[snmpwalk]]. El switch puede enviar su RASlog a un colector central vía `syslogdIpAdd`.
