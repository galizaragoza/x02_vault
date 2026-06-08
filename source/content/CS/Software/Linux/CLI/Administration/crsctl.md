`crsctl` (Cluster Ready Services Control) es la herramienta de línea de comandos de Oracle Grid Infrastructure para administrar **Oracle Clusterware**: el stack de alta disponibilidad sobre el que corren Oracle RAC y Oracle Restart. Gestiona el ciclo de vida de los demonios del clúster (CRS, CSS, EVM, OHAS), los recursos (bases de datos, listeners, VIPs, ASM, servicios), los *voting disks*, los parámetros de heartbeat y el logging. Sustituye y amplía a las antiguas `crs_*` (`crs_stat`, `crs_start`...). La sintaxis general es `crsctl <verbo> <objeto> [opciones]`; muchas operaciones de escritura exigen ejecutarse como `root` (o `grid` para Oracle Restart). En modo single-instance (Oracle Restart) gestiona **OHAS/HAS**; en clúster real gestiona **CRS**.

```
crsctl <comando> <objeto> [<argumentos>]
```

> Binario en `$GRID_HOME/bin/crsctl`. Componentes clave: **CRS** (Cluster Ready Services, orquestador de recursos), **CSS** (Cluster Synchronization Services, membresía/heartbeat), **EVM** (Event Manager), **CTSS** (Cluster Time Synchronization), **OHAS** (Oracle High Availability Services, arranca todo el stack), **GPnP/GIPC/CRF** y demás módulos. El registro central es el **OCR**; el quórum lo dan los **voting disks**.

---

## Sintaxis global y ayuda

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-help` / `-h` | Ayuda general o de un objeto concreto. | `crsctl start -help` |
| `crsctl <verbo> <objeto> -help` | Ayuda contextual de un subcomando. | `crsctl modify resource -help` |
| `-init` | Dirige el comando al stack OHAS (recursos de inicialización `ora.*`). | `crsctl status resource -init` |
| `-f` | Fuerza la operación (en `delete`, `stop`, etc.). | `crsctl delete resource miApp -f` |

---

## crsctl check — comprobación de estado

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl check crs` | Estado de CRS, CSS y EVM en el nodo local. | `crsctl check crs` |
| `crsctl check cluster [-all]` | Estado del CRS en todos los nodos (`-all`) o en nodos concretos. | `crsctl check cluster -all` |
| `crsctl check cluster -n <nodo>` | Estado de CRS en un nodo específico. | `crsctl check cluster -n nodo2` |
| `crsctl check css` | Estado del demonio CSS. | `crsctl check css` |
| `crsctl check evm` | Estado del Event Manager. | `crsctl check evm` |
| `crsctl check ctss` | Estado del servicio de sincronización de tiempo. | `crsctl check ctss` |
| `crsctl check has` | Estado de OHAS (Oracle Restart / single instance). | `crsctl check has` |

---

## crsctl start / stop — control del stack

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl start crs` | Arranca el stack Clusterware en el nodo local. | `crsctl start crs` |
| `crsctl start crs -wait` | Arranca y espera mostrando progreso hasta finalizar. | `crsctl start crs -wait` |
| `crsctl start crs -waithas` | Espera solo a que OHAS arranque. | `crsctl start crs -waithas` |
| `crsctl start crs -noautostart` | Arranca OHAS sin iniciar automáticamente los recursos. | `crsctl start crs -noautostart` |
| `crsctl stop crs` | Detiene el stack en el nodo local de forma ordenada. | `crsctl stop crs` |
| `crsctl stop crs -f` | Fuerza la parada aunque haya recursos colgados. | `crsctl stop crs -f` |
| `crsctl start cluster [-all\|-n <nodos>]` | Arranca CRS en todos los nodos o nodos dados (CRS ya activo). | `crsctl start cluster -all` |
| `crsctl stop cluster [-all\|-n <nodos>] [-f]` | Detiene CRS en todo el clúster o nodos dados. | `crsctl stop cluster -all -f` |
| `crsctl start has` | Arranca OHAS (Oracle Restart). | `crsctl start has` |
| `crsctl stop has [-f]` | Detiene OHAS (Oracle Restart). | `crsctl stop has -f` |

> `start/stop crs` opera sobre el nodo local vía OHAS; `start/stop cluster` opera sobre CRS ya en marcha y admite varios nodos.

---

## crsctl enable / disable / config — autoarranque

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl enable crs` | Habilita el arranque automático de CRS al bootear. | `crsctl enable crs` |
| `crsctl disable crs` | Deshabilita el autoarranque de CRS. | `crsctl disable crs` |
| `crsctl config crs` | Muestra si el autoarranque está habilitado. | `crsctl config crs` |
| `crsctl enable has` | Habilita autoarranque de OHAS (Oracle Restart). | `crsctl enable has` |
| `crsctl disable has` | Deshabilita autoarranque de OHAS. | `crsctl disable has` |
| `crsctl config has` | Estado del autoarranque de OHAS. | `crsctl config has` |

---

## crsctl query — versiones y parches

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl query crs activeversion [-f]` | Versión activa del software del clúster (`-f` muestra detalle de patches). | `crsctl query crs activeversion` |
| `crsctl query crs softwareversion [<nodo>]` | Versión instalada del software en un nodo. | `crsctl query crs softwareversion nodo2` |
| `crsctl query crs releaseversion` | Versión de release del software del nodo local. | `crsctl query crs releaseversion` |
| `crsctl query crs releasepatch` | Nivel de patch del release instalado. | `crsctl query crs releasepatch` |
| `crsctl query crs softwarepatch [<nodo>]` | Nivel de patch del software por nodo. | `crsctl query crs softwarepatch nodo1` |
| `crsctl query crs administrator` | Lista usuarios administradores de CRS. | `crsctl query crs administrator` |

---

## crsctl — gestión de voting disks

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl query css votedisk` | Lista los voting disks y su estado. | `crsctl query css votedisk` |
| `crsctl add css votedisk <path>` | Añade un voting disk (almacenamiento no-ASM). | `crsctl add css votedisk /dev/sdc1` |
| `crsctl delete css votedisk <path\|FUID>` | Elimina un voting disk por path o FUID. | `crsctl delete css votedisk 7a3c...` |
| `crsctl replace votedisk <+diskgroup\|paths>` | Reubica el conjunto de voting disks (típico a un disk group ASM). | `crsctl replace votedisk +CRSDG` |
| `crsctl pin css -n <nodo>` | Fija (pin) un nodo para Clusterware leaf/hub estable. | `crsctl pin css -n nodo1` |
| `crsctl unpin css -n <nodo>` | Quita el pin de un nodo. | `crsctl unpin css -n nodo1` |

---

## crsctl get / set / unset — parámetros CSS y de clúster

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl get css <param>` | Lee un parámetro CSS (`misscount`, `reboottime`, `disktimeout`). | `crsctl get css misscount` |
| `crsctl set css <param> <valor>` | Modifica un parámetro CSS (afecta al heartbeat/fencing). | `crsctl set css misscount 60` |
| `crsctl unset css <param>` | Restaura el valor por defecto de un parámetro CSS. | `crsctl unset css disktimeout` |
| `crsctl get css disktimeout` | Timeout de I/O a los voting disks (s). | `crsctl get css disktimeout` |
| `crsctl get css reboottime` | Tiempo de reinicio tras evicción de nodo (s). | `crsctl get css reboottime` |
| `crsctl get nodename` | Devuelve el nombre del nodo local. | `crsctl get nodename` |
| `crsctl get cluster mode [status]` | Modo del clúster (standard / flex). | `crsctl get cluster mode status` |
| `crsctl set cluster mode <flex>` | Cambia el modo del clúster a Flex. | `crsctl set cluster mode flex` |
| `crsctl get cluster hubsize` | Nº máximo de Hub Nodes (Flex Cluster). | `crsctl get cluster hubsize` |
| `crsctl set cluster hubsize <n>` | Define el tamaño de Hub. | `crsctl set cluster hubsize 32` |
| `crsctl get cluster name` | Nombre del clúster. | `crsctl get cluster name` |
| `crsctl get node role config [-node <n>]` | Rol configurado del nodo (hub/leaf). | `crsctl get node role config -node nodo3` |
| `crsctl set node role {hub\|leaf} -node <n>` | Asigna rol a un nodo (Flex). | `crsctl set node role leaf -node nodo3` |
| `crsctl get cpu equivalency` | Equivalencia de CPU para políticas de servidor. | `crsctl get cpu equivalency` |

---

## crsctl — recursos (resource)

Administración de recursos gestionados por Clusterware (BBDD, listeners, VIPs, ASM, apps de usuario).

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl status resource [-t]` | Estado de todos los recursos (`-t` = tabla compacta). | `crsctl status resource -t` |
| `crsctl status resource <nombre> [-f]` | Estado de un recurso (`-f` = todos los atributos). | `crsctl status resource ora.cssd -f` |
| `crsctl status resource -w "<filtro>"` | Filtra por expresión sobre atributos. | `crsctl status resource -w "TYPE = ora.database.type"` |
| `crsctl start resource <nombre> [-n <nodo>]` | Arranca un recurso (en un nodo concreto con `-n`). | `crsctl start resource miApp -n nodo2` |
| `crsctl stop resource <nombre> [-n <nodo>] [-f]` | Detiene un recurso. | `crsctl stop resource miApp -f` |
| `crsctl relocate resource <nombre> -n <nodo>` | Reubica un recurso a otro nodo. | `crsctl relocate resource miApp -n nodo2` |
| `crsctl add resource <nombre> -type <tipo> [-attr ...]` | Registra un recurso nuevo. | `crsctl add resource miApp -type cluster_resource -attr "ACTION_SCRIPT=/u01/app.sh"` |
| `crsctl modify resource <nombre> -attr "<k>=<v>"` | Modifica atributos de un recurso. | `crsctl modify resource miApp -attr "AUTO_START=always"` |
| `crsctl delete resource <nombre> [-f]` | Elimina un recurso del registro. | `crsctl delete resource miApp -f` |
| `crsctl setperm resource <nombre> -u/-g/-o ...` | Cambia permisos (owner/grupo/otros) del recurso. | `crsctl setperm resource miApp -o oracle` |
| `crsctl getperm resource <nombre>` | Muestra los permisos del recurso. | `crsctl getperm resource miApp` |
| `crsctl eval ...` | Evalúa el efecto de una operación sin ejecutarla (what-if). | `crsctl eval start resource miApp` |

---

## crsctl — tipos de recurso (resource type)

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl status type [<tipo>]` | Lista/estado de los tipos de recurso. | `crsctl status type ora.database.type` |
| `crsctl add type <tipo> -basetype <base> -attr ...` | Define un tipo de recurso nuevo. | `crsctl add type my.app.type -basetype cluster_resource` |
| `crsctl modify type <tipo> -attr ...` | Modifica un tipo de recurso. | `crsctl modify type my.app.type -attr "ATTRIBUTE=START_TIMEOUT,DEFAULT_VALUE=120"` |
| `crsctl delete type <tipo>` | Elimina un tipo de recurso. | `crsctl delete type my.app.type` |

---

## crsctl — servidores y server pools

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl status server [-f]` | Estado de los servidores (nodos) del clúster. | `crsctl status server -f` |
| `crsctl status serverpool [-f]` | Estado de los server pools. | `crsctl status serverpool -f` |
| `crsctl add serverpool <nombre> -attr ...` | Crea un server pool con sus atributos. | `crsctl add serverpool pool1 -attr "MIN_SIZE=1,MAX_SIZE=3"` |
| `crsctl modify serverpool <nombre> -attr ...` | Modifica un server pool. | `crsctl modify serverpool pool1 -attr "MAX_SIZE=4"` |
| `crsctl delete serverpool <nombre>` | Elimina un server pool. | `crsctl delete serverpool pool1` |
| `crsctl setperm serverpool <nombre> ...` | Cambia permisos de un server pool. | `crsctl setperm serverpool pool1 -o grid` |
| `crsctl relocate server <nodo> -serverpool <p>` | Mueve un servidor a otro pool. | `crsctl relocate server nodo3 -serverpool pool1` |

---

## crsctl — logging, tracing y depuración

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl lsmodules <componente>` | Lista los módulos de log de un componente (`crs`, `css`, `evm`). | `crsctl lsmodules crs` |
| `crsctl get log <comp> <módulo>` | Nivel de log actual de un módulo. | `crsctl get log css CSSD` |
| `crsctl set log <comp> "<módulo>=<nivel>"` | Ajusta el nivel de log (0–5). | `crsctl set log crs "CRSRTI=2"` |
| `crsctl set trace <comp> "<módulo>=<nivel>"` | Ajusta el nivel de trace. | `crsctl set trace css "CSSD=3"` |
| `crsctl get trace <comp> <módulo>` | Consulta el nivel de trace. | `crsctl get trace crs CRSCOMM` |

---

## crsctl — OCR y backups del cluster

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `crsctl backup -h` | Ayuda de las operaciones de backup. | `crsctl backup -h` |
| `crsctl query crs autostart` | Configuración de autostart de recursos. | `crsctl query crs autostart` |
| `crsctl stat res -t -init` | Estado de los recursos de inicialización (`ora.*` de OHAS). | `crsctl stat res -t -init` |

> El backup/restauración del **OCR** se hace con `ocrconfig`; los voting disks se respaldan automáticamente en el OCR desde 11.2. `crsctl` los consulta/reemplaza pero no hace backup manual de OCR.

---

## Casos de uso comunes

```bash
# Estado rápido de todo el stack y recursos
crsctl check crs
crsctl status resource -t

# Arranque/parada controlada del nodo local
crsctl start crs -wait
crsctl stop crs -f

# Parar el clúster entero
crsctl stop cluster -all -f

# Verificar versiones tras un upgrade/patch
crsctl query crs activeversion -f
crsctl query crs softwareversion

# Migrar voting disks a un disk group ASM
crsctl replace votedisk +CRSDG
crsctl query css votedisk

# Ajustar el heartbeat (misscount) — operación crítica, planificar ventana
crsctl get css misscount
crsctl set css misscount 60

# Reubicar una base de datos a otro nodo
crsctl relocate resource ora.orcl.db -n nodo2

# Subir verbosidad de CSS para diagnóstico
crsctl lsmodules css
crsctl set log css "CSSD=3"

# Deshabilitar autoarranque antes de mantenimiento del SO
crsctl disable crs
```

Relacionado: [[pcs]], [[systemctl]], [[service]].
