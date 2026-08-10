Comandos de **zoning** de los switches Fibre Channel Brocade (Broadcom), ejecutados dentro del shell de **Fabric OS (FOS)**. El zoning controla qué dispositivos de una SAN pueden comunicarse entre sí, segmentando la fabric de forma análoga a las VLAN en Ethernet. El modelo tiene tres niveles jerárquicos: **alias** (nombre legible para un WWN o puerto), **zone** (grupo de miembros que pueden verse) y **cfg/zone config** (conjunto de zonas que se activa en la fabric). Solo una configuración puede estar *efectiva* (activa) a la vez, y los cambios no son persistentes hasta hacer `cfgSave`. Véase el índice en [[brocade-fos]].

```
admin> <comando> "nombre", "miembro[; miembro...]"
```

> **Flujo estándar:** `aliCreate` → `zoneCreate` → `cfgCreate`/`cfgAdd` → `cfgSave` (persistir) → `cfgEnable` (activar). Los nombres de miembro pueden ser: un **alias**, un **WWN** (`10:00:00:05:1e:...`) o un par **Dominio,Puerto** (`1,5`).

---

# alias

Objeto que asocia un nombre simbólico a uno o varios miembros (WWN o D,P), para no manejar WWN crudos en las zonas.

| Comando | Función | Ejemplo de Sintaxis |
|---------|---------|---------------------|
| `aliCreate` | Crea un alias con sus miembros iniciales. | `aliCreate "srv_esx01", "10:00:00:05:1e:aa:bb:cc"` |
| `aliAdd` | Añade un miembro a un alias existente. | `aliAdd "srv_esx01", "10:00:00:05:1e:dd:ee:ff"` |
| `aliRemove` | Quita un miembro de un alias. | `aliRemove "srv_esx01", "10:00:00:05:1e:dd:ee:ff"` |
| `aliDelete` | Elimina por completo un alias. | `aliDelete "srv_esx01"` |
| `aliShow` | Muestra los alias definidos (acepta patrón con comodín). | `aliShow "srv_*"` |

---

# zone

Grupo de miembros (alias, WWN o D,P) autorizados a comunicarse entre sí. Una zona sin estar incluida en un cfg activo no tiene efecto.

| Comando | Función | Ejemplo de Sintaxis |
|---------|---------|---------------------|
| `zoneCreate` | Crea una zona con sus miembros iniciales. | `zoneCreate "z_esx01_array", "srv_esx01; array_ctrlA"` |
| `zoneAdd` | Añade un miembro a una zona existente. | `zoneAdd "z_esx01_array", "array_ctrlB"` |
| `zoneRemove` | Quita un miembro de una zona. | `zoneRemove "z_esx01_array", "array_ctrlB"` |
| `zoneDelete` | Elimina por completo una zona. | `zoneDelete "z_esx01_array"` |
| `zoneShow` | Muestra las zonas definidas (acepta patrón). | `zoneShow "z_esx*"` |
| `zoneCreate --peerzone` | Crea una *peer zone* (un principal —típicamente el storage— visible por todos los miembros, que no se ven entre sí). | `zoneCreate --peerzone "pz_array" -principal "array_ctrlA" -members "srv1; srv2"` |
| `zoneObjectRename` | Renombra un objeto de zoning (alias, zona o cfg). | `zoneObjectRename "z_old", "z_new"` |
| `zoneObjectExpunge` | Borra un objeto de zoning y todas sus referencias en otros objetos. | `zoneObjectExpunge "srv_esx01"` |
| `zone --validate` | Valida la configuración: detecta miembros no resueltos o errores. | `zone --validate "cfg_prod"` |

---

# cfg (zone configuration)

Conjunto de zonas. La fabric mantiene una *defined configuration* (todas las definidas, persistida en flash) y una *effective configuration* (la única activa). `cfgEnable` activa una; `cfgSave` persiste la definida.

| Comando | Función | Ejemplo de Sintaxis |
|---------|---------|---------------------|
| `cfgCreate` | Crea una configuración con sus zonas iniciales. | `cfgCreate "cfg_prod", "z_esx01_array; z_esx02_array"` |
| `cfgAdd` | Añade una zona a una configuración existente. | `cfgAdd "cfg_prod", "z_esx03_array"` |
| `cfgRemove` | Quita una zona de una configuración. | `cfgRemove "cfg_prod", "z_esx03_array"` |
| `cfgDelete` | Elimina por completo una configuración. | `cfgDelete "cfg_test"` |
| `cfgEnable` | Activa una configuración como efectiva (pide confirmación; propaga a toda la fabric). | `cfgEnable "cfg_prod"` |
| `cfgDisable` | Desactiva el zoning efectivo (deja la fabric **sin** zoning; según `defZone`, puede abrir todo el acceso). | `cfgDisable` |
| `cfgSave` | Persiste la configuración definida en la flash de todos los switches de la fabric. | `cfgSave` |
| `cfgShow` | Muestra la config definida y la efectiva (acepta patrón). | `cfgShow "cfg_prod"` |
| `cfgActvShow` | Muestra solo la configuración efectiva (la activa). | `cfgActvShow` |
| `cfgClear` | Borra **toda** la base de datos de zoning (alias, zonas, cfg). Requiere `cfgSave`/`cfgDisable` posterior. | `cfgClear` |
| `cfgTransAbort` | Aborta la transacción de zoning pendiente (descarta cambios no guardados). | `cfgTransAbort` |
| `cfgTransShow` | Muestra si hay una transacción de zoning abierta. | `cfgTransShow` |

---

## defZone (política por defecto)

Define el comportamiento de los dispositivos **no incluidos** en ninguna zona efectiva, o cuando no hay zoning activo.

| Comando | Función | Ejemplo de Sintaxis |
|---------|---------|---------------------|
| `defZone --noaccess` | Política restrictiva: sin zoning, nadie se comunica (recomendado). | `defZone --noaccess` |
| `defZone --allaccess` | Política permisiva: sin zoning, todos se ven (riesgo de interferencias). | `defZone --allaccess` |
| `defZone --show` | Muestra la política por defecto actual. | `defZone --show` |

---

## Sesión típica completa

```text
# 1. Definir alias para servidor y controladoras del array
admin> aliCreate "srv_esx01", "10:00:00:05:1e:aa:bb:cc"
admin> aliCreate "array_ctrlA", "50:06:01:60:00:00:aa:01"
admin> aliCreate "array_ctrlB", "50:06:01:68:00:00:aa:01"

# 2. Crear la zona que los une (single-initiator / multi-target)
admin> zoneCreate "z_esx01_array", "srv_esx01; array_ctrlA; array_ctrlB"

# 3. Crear o ampliar la configuración
admin> cfgCreate "cfg_prod", "z_esx01_array"
   # (si ya existe: cfgAdd "cfg_prod", "z_esx01_array")

# 4. Persistir en flash
admin> cfgSave

# 5. Activar como configuración efectiva en toda la fabric
admin> cfgEnable "cfg_prod"

# 6. Verificar
admin> cfgActvShow
```

> **Buena práctica:** zonas *single-initiator* (un solo HBA de servidor por zona, con uno o varios targets de storage), y `defZone --noaccess` como política base.
