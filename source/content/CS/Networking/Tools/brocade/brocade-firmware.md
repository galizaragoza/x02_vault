Gestión de **firmware y configuración** en Fabric OS: consulta y actualización del firmware (`firmwareDownload`, con descarga de paquetes desde host FTP/SCP), respaldo y restauración de la configuración (`configUpload`/`configDownload`), restablecimiento a valores de fábrica, y reinicios. En directores con dos CP, la actualización es *non-disruptive* (HA). Véase el índice en [[brocade-fos]]; las licencias se tratan en [[brocade-switch]].

---

# Firmware: consulta y estado

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `version` | Versión de Fabric OS, kernel y fecha de compilación. | `version` |
| `firmwareShow` | Versión de firmware en cada partición/CP. | `firmwareShow` |
| `firmwareDownloadStatus` | Progreso/resultado de la última descarga de firmware. | `firmwareDownloadStatus` |
| `firmwareKeyShow` | Muestra la clave pública usada para validar firmware firmado. | `firmwareKeyShow` |

---

# Firmware: actualización

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `firmwareDownload` | Descarga e instala firmware desde un host remoto (interactivo o por parámetros). | `firmwareDownload -s -p scp 10.0.0.9,admin,/fw/v9.1,clave` |
| `firmwareCommit` | Confirma el firmware en ambas particiones tras validar la nueva versión. | `firmwareCommit` |
| `firmwareRestore` | Revierte a la versión anterior si la nueva falla (rollback). | `firmwareRestore` |
| `firmwareCleanInstall` | Instalación limpia (recovery), descartando estado previo. | `firmwareCleanInstall` |

> Flujo HA típico en directores: `firmwareDownload` actualiza el CP standby, hace `haFailover` y luego el otro CP, sin cortar tráfico. Verificar con `firmwareShow` y `firmwareDownloadStatus`.

---

# Configuración: respaldo y restauración

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `configShow` | Muestra parámetros de configuración (filtrable por sección). | `configShow "zoning"` |
| `configUpload` | Sube (respalda) la configuración a un host remoto. | `configUpload -p scp 10.0.0.9,admin,/cfg/sw01.txt` |
| `configDownload` | Descarga (restaura) una configuración desde un host. | `configDownload -p scp 10.0.0.9,admin,/cfg/sw01.txt` |
| `configDefault` | Restablece la configuración (no el zoning) a valores de fábrica. | `configDefault` |
| `configRemove` | Elimina un fichero de configuración guardado localmente. | `configRemove` |

> `configUpload`/`configDownload` aceptan `-vf` para incluir la configuración de Virtual Fabrics y `-all` para chassis + switches. El zoning forma parte del upload; ver [[brocade-zoning]].

---

# Reinicio y recuperación

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `reboot` | Reinicio completo con POST (disruptivo en switch sencillo). | `reboot` |
| `fastBoot` | Reinicio omitiendo el POST (más rápido). | `fastBoot` |
| `haFailover` | Conmuta al CP standby (no disruptivo en directores). | `haFailover` |
| `sysShutdown` | Apagado ordenado del sistema. | `sysShutdown` |
| `reboot -f` | Reinicio forzado. | `reboot -f` |
