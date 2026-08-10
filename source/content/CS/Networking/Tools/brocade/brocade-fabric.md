Gestión de la **fabric** y del **enrutado** en Fabric OS: descubrimiento de switches, elección del switch principal, enlaces inter-switch (ISL/trunk), topología y rutas FSPF, balanceo dinámico de carga (DLS), enrutado entre fabrics (FC Routing / FCR), modo *Access Gateway* y utilidades de verificación de conectividad (`fcPing`, `fcTraceRoute`). El name server (`nsShow`) lista los dispositivos finales. Véase el índice en [[brocade-fos]]; el control de visibilidad entre dispositivos está en [[brocade-zoning]].

---

# Descubrimiento de fabric y name server

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `fabricShow` | Lista los switches de la fabric: Domain ID, WWN, IP y nombre. | `fabricShow` |
| `fabStateShow` | Estado de formación de la fabric (build/stable). | `fabStateShow` |
| `fabricPrincipal` | Muestra o fuerza la elección del switch principal. | `fabricPrincipal -f 1` |
| `nsShow` | Dispositivos N_Port registrados localmente. | `nsShow` |
| `nsAllShow` | Todos los dispositivos de toda la fabric. | `nsAllShow` |
| `nsCamShow` | Caché del name server distribuido (remotos). | `nsCamShow` |
| `nodeFind` | Busca un dispositivo por WWN/PID en la fabric. | `nodeFind "10:00:00:05:1e:aa:bb:cc"` |

---

# Enlaces inter-switch (ISL) y trunking

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `islShow` | Lista los ISLs activos y sus extremos. | `islShow` |
| `trunkShow` | Muestra los grupos de trunk y sus miembros. | `trunkShow` |
| `lsanZoneShow` | Zonas LSAN (dispositivos exportados vía FCR). | `lsanZoneShow -s` |
| `portCfgTrunkPort` | Habilita trunking por puerto (ver [[brocade-ports]]). | `portCfgTrunkPort 12 1` |
| `trunkDebug` | Diagnostica por qué un puerto no se une al trunk. | `trunkDebug 12 14` |

---

# Topología y enrutado FSPF

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `topologyShow` | Topología FSPF: dominios alcanzables y coste de ruta. | `topologyShow` |
| `uRouteShow` | Rutas unicast actuales por puerto/dominio. | `uRouteShow` |
| `fspfShow` | Parámetros del protocolo de enrutado FSPF. | `fspfShow` |
| `linkCost` | Muestra o fija el coste FSPF de un enlace. | `linkCost 12 1000` |
| `bcastShow` | Árbol de distribución de broadcast. | `bcastShow` |
| `dlsShow` | Estado del balanceo dinámico de carga (DLS). | `dlsShow` |
| `dlsSet` / `dlsReset` | Activa/desactiva DLS. | `dlsSet` |
| `aptPolicy` | Política de enrutado (port-based / device-based / exchange-based). | `aptPolicy 3` |

---

# FC Routing (FCR) — enrutado entre fabrics

Conecta fabrics distintas sin fusionarlas (requiere licencia Integrated Routing y EX_Ports).

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `fcrConfigure` | Configura los parámetros del backbone de enrutado. | `fcrConfigure` |
| `fcrFabricShow` | Lista las fabrics EX_Port conectadas al router. | `fcrFabricShow` |
| `fcrRouteShow` | Rutas inter-fabric activas. | `fcrRouteShow` |
| `fcrPhyDevShow` | Dispositivos físicos visibles para el FCR. | `fcrPhyDevShow` |
| `fcrProxyDevShow` | Dispositivos proxy creados en cada fabric. | `fcrProxyDevShow` |
| `fcrXlateConfig` | Configura los dominios de traducción (xlate). | `fcrXlateConfig 1 2 0x01` |
| `portCfgEXPort` | Configura un puerto como EX_Port para FCR. | `portCfgEXPort 12 -a 1 -f 10` |

---

# Access Gateway (AG)

Modo en el que el switch presenta sus puertos como N_Ports a la fabric (no consume Domain ID).

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `ag --modeshow` | Indica si el modo Access Gateway está activo. | `ag --modeshow` |
| `ag --modeenable` / `--modedisable` | Habilita/deshabilita el modo AG (reinicia). | `ag --modeenable` |
| `ag --mapshow` | Muestra el mapeo F_Port → N_Port. | `ag --mapshow` |
| `ag --mapset` | Define el mapeo de F_Ports a un N_Port. | `ag --mapset 4 "12;13"` |
| `agShow` | (Desde el switch de la fabric) lista los AG conectados. | `agShow` |

---

# Verificación de conectividad

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `fcPing` | "Ping" FC entre dos dispositivos por WWN/PID. | `fcPing "10:00:..aa" "50:06:..01"` |
| `fcTraceRoute` | Traza la ruta FC entre dos dispositivos. | `fcTraceRoute -i 0x010f00 0x020a00` |
| `pathInfo` | Detalle salto a salto de la ruta (latencia/coste). | `pathInfo 2` |
