La **CLI de ONTAP** es la interfaz de línea de comandos de los sistemas de almacenamiento NetApp (clustered Data ONTAP). Es una shell jerárquica organizada en *directorios de comandos* (`storage`, `volume`, `network`, `vserver`, `cluster`...), no una herramienta de flags al estilo Unix. Gestiona agregados, volúmenes, LUNs, SVMs (Storage Virtual Machines), protocolos NAS/SAN (NFS, CIFS/SMB, iSCSI, FC) y replicación (SnapMirror). El acceso se realiza por SSH al LIF de gestión del clúster; el comportamiento depende del **nivel de privilegio** activo (admin/advanced/diagnostic). Esta guía documenta el marco de la CLI (navegación, privilegios, modificadores de consulta y salida) y las familias de comandos principales; no es un catálogo exhaustivo de todos los comandos (hay miles).

> Sintaxis general: `<directorio> <subdirectorio> <comando> -<parámetro> <valor>`
> Ejemplo: `volume create -vserver svm1 -volume vol1 -aggregate aggr1 -size 100GB`

# Navegación e interacción de la shell

La CLI mantiene un contexto de directorio; los comandos pueden ejecutarse de forma relativa o absoluta.

| **Comando / Tecla** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `?` o `help` | Lista los comandos/subdirectorios disponibles en el contexto actual. | `storage aggregate ?` |
| `<Tab>` | Autocompleta comandos, parámetros y valores. | `vol cr<Tab>` |
| `top` | Vuelve al nivel raíz del árbol de comandos. | `top` |
| `up` o `..` | Sube un nivel en la jerarquía. | `up` |
| `man <comando>` | Muestra la página de manual del comando. | `man volume create` |
| `history` | Lista los comandos ejecutados en la sesión. | `history` |
| `rows <n>` | Fija el nº de filas antes de paginar (`0` desactiva la paginación). | `rows 0` |
| `exit` o `Ctrl-D` | Cierra la sesión de la CLI. | `exit` |

# Niveles de privilegio y entorno de sesión

`set` controla el contexto de ejecución. Los niveles `advanced` y `diagnostic` exponen comandos potencialmente disruptivos.

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `set -privilege admin` | Nivel por defecto; operaciones rutinarias. | `set -privilege admin` |
| `set -privilege advanced` | Habilita comandos avanzados (requiere confirmación). | `set -privilege advanced` |
| `set -privilege diagnostic` | Nivel de diagnóstico, solo bajo guía de soporte. | `set -privilege diagnostic` |
| `set -confirmations on\|off` | Activa/desactiva las solicitudes de confirmación. | `set -confirmations off` |
| `set -units <u>` | Fija las unidades de tamaño de salida (`auto`/`B`/`KB`/`MB`/`GB`...). | `set -units GB` |
| `set -rows <n>` | Equivalente a `rows`: control de paginación. | `set -rows 0` |
| `set -vserver <svm>` | Fija la SVM de contexto para los comandos posteriores. | `set -vserver svm1` |

# Modificadores de salida y consulta

Aplicables como parámetros a los comandos `show` y de consulta de cualquier directorio.

| **Modificador** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-instance` | Muestra todos los campos de cada objeto (vista detallada). | `volume show -instance` |
| `-fields <c1,c2>` | Muestra solo los campos indicados. | `volume show -fields size,state,aggregate` |
| `-<campo> <patrón>` | Filtra por valor de un campo (consulta). | `volume show -state offline` |
| `*` (comodín) | Coincide con cualquier valor en una consulta. | `volume show -vserver svm*` |
| `!` (negación) | Excluye los objetos que coinciden con el valor. | `volume show -state !online` |
| `<`, `>`, `<=`, `>=` | Operadores de comparación en consultas numéricas. | `volume show -size >100GB` |
| `0..100` (rango) | Coincide con un rango de valores. | `volume show -percent-used 80..100` |
| `\|` (OR) | Coincide con varios valores alternativos. | `volume show -state "offline\|restricted"` |

# `cluster` — gestión del clúster

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `cluster show` | Estado de los nodos del clúster. | `cluster show` |
| `cluster ha show` | Estado de la alta disponibilidad. | `cluster ha show` |
| `cluster peer show` | Relaciones de peering entre clústeres. | `cluster peer show` |
| `cluster ring show` | Estado de los rings de replicación de configuración. | `cluster ring show` |
| `cluster identity show` | Identidad (nombre, UUID) del clúster. | `cluster identity show` |

# `system` / `node` — nodos y hardware

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `system node show` | Lista los nodos y su estado/salud. | `system node show` |
| `system node reboot` | Reinicia un nodo. | `system node reboot -node node1` |
| `system node run` | Ejecuta comandos del nodeshell (estilo 7-mode). | `system node run -node node1 sysconfig -a` |
| `system health status show` | Estado global de salud del sistema. | `system health status show` |
| `system license show` | Licencias instaladas. | `system license show` |
| `system services firewall show` | Configuración del firewall de gestión. | `system services firewall show` |

# `storage aggregate` — agregados (pools RAID)

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `storage aggregate show` | Lista los agregados y su uso. | `storage aggregate show` |
| `storage aggregate create` | Crea un agregado a partir de discos. | `storage aggregate create -aggregate aggr1 -diskcount 12 -node node1` |
| `storage aggregate add-disks` | Amplía un agregado con más discos. | `storage aggregate add-disks -aggregate aggr1 -diskcount 6` |
| `storage aggregate offline\|online` | Cambia el estado del agregado. | `storage aggregate offline -aggregate aggr1` |
| `storage aggregate delete` | Elimina un agregado vacío. | `storage aggregate delete -aggregate aggr1` |
| `storage disk show` | Inventario de discos físicos. | `storage disk show -fields container-type` |

# `volume` — volúmenes FlexVol

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `volume show` | Lista los volúmenes. | `volume show -vserver svm1` |
| `volume create` | Crea un volumen en un agregado y SVM. | `volume create -vserver svm1 -volume vol1 -aggregate aggr1 -size 500GB` |
| `volume modify` | Modifica atributos (tamaño, política, autosize). | `volume modify -volume vol1 -size 1TB` |
| `volume offline\|online` | Cambia el estado del volumen. | `volume offline -volume vol1` |
| `volume delete` | Elimina un volumen offline. | `volume delete -volume vol1` |
| `volume snapshot create` | Crea un snapshot del volumen. | `volume snapshot create -volume vol1 -snapshot snap1` |
| `volume snapshot restore` | Restaura el volumen desde un snapshot. | `volume snapshot restore -volume vol1 -snapshot snap1` |
| `volume efficiency on` | Activa deduplicación/compresión. | `volume efficiency on -volume vol1` |
| `volume qtree create` | Crea un qtree dentro de un volumen. | `volume qtree create -volume vol1 -qtree qt1` |

# `vserver` — Storage Virtual Machines (SVM) y protocolos NAS

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `vserver show` | Lista las SVMs. | `vserver show` |
| `vserver create` | Crea una SVM. | `vserver create -vserver svm1 -rootvolume root_vol -aggregate aggr1` |
| `vserver nfs create\|show` | Configura/consulta el servicio NFS de la SVM. | `vserver nfs create -vserver svm1 -v3 enabled` |
| `vserver cifs create\|show` | Configura/consulta el servicio CIFS/SMB. | `vserver cifs create -vserver svm1 -cifs-server SMB1 -domain dominio.local` |
| `vserver export-policy create` | Crea una política de exportación NFS. | `vserver export-policy create -vserver svm1 -policyname pol1` |
| `vserver cifs share create` | Crea un recurso compartido SMB. | `vserver cifs share create -vserver svm1 -share-name datos -path /vol1` |
| `vserver peer show` | Relaciones de peering entre SVMs. | `vserver peer show` |

# `network` — interfaces lógicas (LIF), puertos y rutas

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `network interface show` | Lista las LIFs y sus IPs/puertos. | `network interface show` |
| `network interface create` | Crea una LIF. | `network interface create -vserver svm1 -lif lif1 -address 10.0.0.10 -netmask 255.255.255.0 -home-node node1 -home-port e0c` |
| `network interface migrate` | Migra una LIF a otro puerto/nodo. | `network interface migrate -vserver svm1 -lif lif1 -destination-node node2` |
| `network port show` | Estado de los puertos físicos. | `network port show` |
| `network route create` | Crea una ruta estática. | `network route create -vserver svm1 -destination 0.0.0.0/0 -gateway 10.0.0.1` |
| `network ping` | Prueba de conectividad desde una LIF. | `network ping -lif lif1 -vserver svm1 -destination 8.8.8.8` |

# SAN: `lun`, `igroup` y `fcp`/`iscsi`

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `lun show` | Lista las LUNs. | `lun show -vserver svm1` |
| `lun create` | Crea una LUN. | `lun create -vserver svm1 -path /vol/vol1/lun1 -size 100GB -ostype linux` |
| `lun map` | Mapea una LUN a un igroup. | `lun map -vserver svm1 -path /vol/vol1/lun1 -igroup ig1` |
| `lun resize` | Redimensiona una LUN. | `lun resize -vserver svm1 -path /vol/vol1/lun1 -size 200GB` |
| `igroup create` | Crea un grupo de iniciadores. | `igroup create -vserver svm1 -igroup ig1 -protocol iscsi -ostype linux` |
| `igroup add` | Añade un iniciador (IQN/WWPN) al igroup. | `igroup add -vserver svm1 -igroup ig1 -initiator iqn.1994-05.com.host:1` |
| `vserver iscsi show` | Estado del servicio iSCSI. | `vserver iscsi show -vserver svm1` |
| `vserver fcp show` | Estado del servicio FC. | `vserver fcp show -vserver svm1` |

# `snapmirror` — replicación y recuperación

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `snapmirror show` | Estado de las relaciones de replicación. | `snapmirror show` |
| `snapmirror create` | Crea una relación SnapMirror. | `snapmirror create -source-path svm1:vol1 -destination-path svm2:vol1_dr -type DP` |
| `snapmirror initialize` | Realiza la transferencia base inicial. | `snapmirror initialize -destination-path svm2:vol1_dr` |
| `snapmirror update` | Fuerza una transferencia incremental. | `snapmirror update -destination-path svm2:vol1_dr` |
| `snapmirror break` | Rompe la relación (destino pasa a R/W para DR). | `snapmirror break -destination-path svm2:vol1_dr` |
| `snapmirror resync` | Resincroniza tras una rotura. | `snapmirror resync -destination-path svm2:vol1_dr` |

# `security` — usuarios, roles y autenticación

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `security login show` | Lista las cuentas de gestión. | `security login show` |
| `security login create` | Crea una cuenta de acceso. | `security login create -user-or-group-name admin2 -application ssh -authentication-method password -role admin` |
| `security login role show` | Lista los roles RBAC y sus permisos. | `security login role show` |
| `security certificate show` | Muestra los certificados instalados. | `security certificate show` |
| `security ssl show` | Configuración SSL de las SVMs. | `security ssl show` |

# `statistics`, `event` y `job` — monitorización y diagnóstico

| **Comando** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `statistics show` | Muestra contadores de rendimiento en vivo. | `statistics show -object volume -counter total_ops` |
| `statistics show-periodic` | Reporta estadísticas a intervalos regulares. | `statistics show-periodic -object volume -instance vol1` |
| `qos statistics performance show` | Rendimiento por workload (latencia/IOPS/throughput). | `qos statistics performance show` |
| `event log show` | Muestra el registro de eventos del sistema (EMS). | `event log show -severity ERROR` |
| `event status show` | Estado de las notificaciones de evento. | `event status show` |
| `job show` | Lista los trabajos en ejecución/en cola. | `job show` |
| `job stop` | Detiene un trabajo en curso. | `job stop -id 1024` |

## Notas

- Los nombres de directorio admiten abreviaturas mientras no sean ambiguos (`vol` → `volume`, `net int show` → `network interface show`).
- En ONTAP 9 existe además **System Manager** (GUI/REST) y la **API REST**; la CLI sigue siendo la referencia completa para automatización vía SSH.
- Comandos en niveles `advanced`/`diagnostic` pueden afectar a datos en producción: confirmar el privilegio activo con `set` antes de operar.
- Relacionada (almacenamiento en red Linux): [[NFS]] y montajes [[(u)mount]].
