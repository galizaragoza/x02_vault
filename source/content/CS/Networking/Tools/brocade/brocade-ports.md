Gestión de **puertos** en Fabric OS: estado y configuración persistente, velocidad, tipo de puerto (F/E/EX/L), larga distancia, *trunking*, QoS, *credit recovery* y diagnóstico de óptica (SFP), además de las estadísticas y contadores de error por puerto. Los puertos se referencian por índice (`12`) o, en directores, por `slot/port` (`3/12`). Véase el índice en [[brocade-fos]].

---

# Estado e información de puerto

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `switchShow` | Vista global: tipo, estado y dispositivo conectado de cada puerto. | `switchShow` |
| `portShow` | Detalle de un puerto: estado, velocidad, flags, WWN conectado. | `portShow 12` |
| `portCfgShow` | Muestra la configuración persistente de uno o todos los puertos. | `portCfgShow 12` |
| `portName` | Asigna o muestra el nombre simbólico de un puerto. | `portName 12 "esx01_hba0"` |
| `portLoginShow` | Sesiones de login (FLOGI/PLOGI) activas en el puerto. | `portLoginShow 12` |
| `portCamShow` | Contenido de la CAM de filtrado del puerto. | `portCamShow 12` |
| `sfpShow` | Diagnóstico de la óptica SFP (potencia Tx/Rx, temperatura, vendor). | `sfpShow 12` |

---

# Habilitación y estado administrativo

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `portEnable` | Habilita un puerto (no persistente tras reinicio). | `portEnable 12` |
| `portDisable` | Deshabilita un puerto. | `portDisable 12` |
| `portCfgPersistentEnable` | Habilita un puerto de forma **persistente**. | `portCfgPersistentEnable 12` |
| `portCfgPersistentDisable` | Deshabilita un puerto de forma persistente. | `portCfgPersistentDisable 12` |
| `portCfgDefault` | Restablece toda la configuración del puerto a valores de fábrica. | `portCfgDefault 12` |
| `portCfgAutoDisable` | Auto-deshabilita el puerto ante eventos definidos (errores/loss of sync). | `portCfgAutoDisable --enable 12` |
| `portDecom` | Decommission ordenado de un puerto E_Port (mueve tráfico antes). | `portDecom 12` |

---

# Velocidad y tipo de puerto

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `portCfgSpeed` | Fija la velocidad (`0`=auto, `2`/`4`/`8`/`16`/`32` Gbps). | `portCfgSpeed 12 16` |
| `portCfgEport` | Habilita/deshabilita la capacidad E_Port (ISL) del puerto. | `portCfgEport 12 1` |
| `portCfgGport` | Bloquea el puerto como G_Port (sin negociar E/F). | `portCfgGport 12 1` |
| `portCfgLport` | Configura el puerto como L_Port (loop). | `portCfgLport 12 1` |
| `portCfgEXPort` | Configura el puerto como EX_Port (FC Routing; ver [[brocade-fabric]]). | `portCfgEXPort 12 -a 1 -f 10` |
| `portCfgNPort` | Configura el puerto en modo N_Port (Access Gateway). | `portCfgNPort 12 1` |
| `portCfgISLMode` | Activa el modo ISL R_RDY (control de flujo legado). | `portCfgISLMode 12 1` |

---

# Larga distancia, trunking y rendimiento

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `portCfgLongDistance` | Configura el modo de larga distancia y los buffer credits. | `portCfgLongDistance 12 LD 1 100` |
| `portCfgFportBuffers` | Asigna buffers a un F_Port. | `portCfgFportBuffers 12 1 40` |
| `portCfgTrunkPort` | Habilita/deshabilita el *trunking* en el puerto. | `portCfgTrunkPort 12 1` |
| `portCfgQos` | Habilita QoS (priorización de tráfico) en el puerto. | `portCfgQos --enable 12` |
| `portCfgCreditRecovery` | Activa la recuperación de créditos de buffer perdidos. | `portCfgCreditRecovery --enable 12` |
| `portCfgFillWord` | Selecciona el *fill word* (ARB/IDLE) en enlaces 8G. | `portCfgFillWord 12 3` |
| `portCfgEncrypt` / `portCfgCompress` | Activa cifrado/compresión en ISLs (requiere licencia). | `portCfgEncrypt --enable 12` |

---

# Estadísticas, errores y logs de puerto

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `portErrShow` | Tabla resumida de errores por puerto (CRC, enc_out, loss_sync…). | `portErrShow` |
| `portStatsShow` | Contadores detallados de un puerto. | `portStatsShow 12` |
| `portStats64Show` | Contadores de 64 bits (no se desbordan). | `portStats64Show 12` |
| `portStatsClear` | Pone a cero los contadores de un puerto. | `portStatsClear 12` |
| `portPerfShow` | Throughput en tiempo real por puerto (Tx/Rx). | `portPerfShow` |
| `portLogShow` | Muestra el log circular de eventos del puerto. | `portLogShow` |
| `portLogDump` | Vuelca el port log en formato extendido. | `portLogDump` |
| `portLogClear` | Borra el port log. | `portLogClear` |

---

# Pruebas de puerto (`portCmd` y diagnóstico)

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `portCmd --portping` | Envía ECHO a un dispositivo conectado para verificar conectividad. | `portCmd --portping 12 -n 5` |
| `portCmd --porttrace` | Traza la ruta desde un puerto a un destino. | `portCmd --porttrace 12 -d 0x010f00` |
| `portLoopbackTest` | Prueba de bucle interno del puerto (offline). | `portLoopbackTest -nframes 100` |
| `spinFab` | Prueba de estrés de los ISLs entre switches. | `spinFab` |
