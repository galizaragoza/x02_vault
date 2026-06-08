pcs (Pacemaker/Corosync Configuration System) es la herramienta de línea de comandos para crear y administrar clústeres de alta disponibilidad en Linux basados en Pacemaker (gestor de recursos) y Corosync (capa de membresía y mensajería). Permite configurar nodos, recursos, restricciones, fencing (STONITH), quórum y propiedades del clúster desde un único punto, propagando la configuración (CIB y `corosync.conf`) a todos los nodos. Sustituye al uso manual de `crm`, `crm_mon`, `cibadmin` y la edición directa de ficheros de configuración. Requiere el demonio `pcsd` corriendo en cada nodo y autenticación previa entre ellos.

# Opciones globales
Opciones válidas en cualquier subcomando de pcs.

| **Flag**              | **Descripción**                                                              | **Ejemplo**                                          |
| --------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------- |
| `-h`, `--help`        | Muestra ayuda del comando o subcomando.                                      | `pcs resource --help`                                |
| `--version`           | Muestra la versión de pcs.                                                    | `pcs --version`                                      |
| `-f <file>`           | Opera sobre un fichero CIB en lugar del clúster activo (modo offline).       | `pcs -f cib.xml resource create ...`                 |
| `--debug`             | Salida detallada de depuración (comandos internos ejecutados).               | `pcs --debug cluster start`                          |
| `--request-timeout=<s>` | Timeout en segundos para peticiones HTTP a nodos remotos (por defecto 60).  | `pcs --request-timeout=120 cluster setup ...`        |
| `--force`             | Fuerza la operación ignorando avisos/errores no críticos.                    | `pcs resource delete --force web`                    |

# pcs cluster
Configuración y control del clúster en su conjunto: creación, arranque, parada, autenticación de nodos y gestión de `corosync.conf`.

| **Comando / Flag**                  | **Descripción**                                                          | **Ejemplo**                                                              |
| ----------------------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------------------------- |
| `setup <nombre> <nodos>`            | Crea un clúster nuevo y genera `corosync.conf`.                          | `pcs cluster setup hacl node1 node2`                                     |
| `setup ... --start`                 | Crea y arranca el clúster inmediatamente.                               | `pcs cluster setup hacl node1 node2 --start`                            |
| `setup ... --enable`                | Habilita arranque del clúster al inicio del sistema.                     | `pcs cluster setup hacl node1 node2 --enable`                            |
| `setup ... --force`                 | Sobrescribe configuración existente.                                    | `pcs cluster setup hacl node1 node2 --force`                            |
| `setup ... transport <tipo>`        | Define el transporte (`knet`, `udp`, `udpu`).                           | `pcs cluster setup hacl node1 node2 transport knet`                     |
| `start`                             | Arranca Corosync y Pacemaker en el nodo local.                          | `pcs cluster start`                                                      |
| `start --all`                       | Arranca el clúster en todos los nodos.                                  | `pcs cluster start --all`                                                |
| `start <nodo>`                      | Arranca el clúster en un nodo concreto.                                 | `pcs cluster start node2`                                                |
| `stop`                              | Detiene Corosync y Pacemaker en el nodo local.                         | `pcs cluster stop`                                                       |
| `stop --all`                        | Detiene el clúster en todos los nodos.                                 | `pcs cluster stop --all`                                                 |
| `stop --force`                      | Fuerza la parada aunque queden recursos activos.                       | `pcs cluster stop --force`                                               |
| `kill`                              | Mata forzosamente los procesos del clúster en el nodo local.          | `pcs cluster kill`                                                       |
| `enable [--all]`                    | Habilita el arranque automático al iniciar el sistema.                 | `pcs cluster enable --all`                                               |
| `disable [--all]`                   | Deshabilita el arranque automático.                                    | `pcs cluster disable --all`                                              |
| `auth <nodos>`                      | Autentica nodos vía pcsd (versiones antiguas; ver `pcs host auth`).     | `pcs cluster auth node1 node2 -u hacluster`                              |
| `status`                            | Muestra estado resumido del clúster.                                    | `pcs cluster status`                                                     |
| `pcsd-status`                       | Estado del demonio pcsd en los nodos.                                  | `pcs cluster pcsd-status`                                                |
| `sync`                              | Sincroniza `corosync.conf` al resto de nodos.                          | `pcs cluster sync`                                                       |
| `cib [<file>]`                      | Vuelca el CIB (XML) por stdout o a fichero.                            | `pcs cluster cib backup.xml`                                             |
| `cib-push <file>`                   | Aplica un CIB modificado al clúster.                                   | `pcs cluster cib-push backup.xml`                                        |
| `cib-upgrade`                       | Actualiza el esquema del CIB a la última versión.                       | `pcs cluster cib-upgrade`                                                |
| `edit`                              | Edita el CIB en `$EDITOR` y lo aplica al guardar.                      | `pcs cluster edit`                                                       |
| `node add <nodo>`                   | Añade un nodo al clúster.                                              | `pcs cluster node add node3`                                             |
| `node remove <nodo>`                | Elimina un nodo del clúster.                                            | `pcs cluster node remove node3`                                          |
| `node add-remote <host>`            | Añade un nodo remoto (Pacemaker Remote).                              | `pcs cluster node add-remote remote1`                                    |
| `node add-guest <host>`             | Añade un nodo guest (recurso VM como nodo remoto).                     | `pcs cluster node add-guest vm1 web-vm`                                  |
| `link add`                          | Añade un enlace (interfaz) de Corosync.                                | `pcs cluster link add node1=10.0.1.1 node2=10.0.1.2`                     |
| `link remove <id>`                  | Elimina un enlace de Corosync.                                          | `pcs cluster link remove 1`                                              |
| `destroy`                           | Destruye la configuración del clúster en el nodo local.               | `pcs cluster destroy`                                                    |
| `destroy --all`                     | Destruye la configuración en todos los nodos.                          | `pcs cluster destroy --all`                                              |
| `verify [-V]`                       | Verifica la validez de la configuración del CIB.                       | `pcs cluster verify -V`                                                  |
| `report <dest>`                     | Genera un informe de diagnóstico del clúster.                          | `pcs cluster report --from "2026-06-01" /tmp/rep`                        |
| `corosync`                          | Muestra el `corosync.conf` en uso.                                     | `pcs cluster corosync`                                                   |
| `reload corosync`                   | Recarga la configuración de Corosync.                                  | `pcs cluster reload corosync`                                            |

# pcs host
Gestión de la autenticación entre nodos (pcsd) en pcs moderno (≥0.10).

| **Comando / Flag**       | **Descripción**                                              | **Ejemplo**                                          |
| ------------------------ | ----------------------------------------------------------- | ---------------------------------------------------- |
| `auth <hosts>`           | Autentica el nodo local frente a otros hosts vía pcsd.      | `pcs host auth node1 node2`                          |
| `auth ... -u <usuario>`  | Especifica el usuario (normalmente `hacluster`).            | `pcs host auth node1 node2 -u hacluster`             |
| `auth ... -p <pass>`     | Especifica la contraseña (evitar en producción).           | `pcs host auth node1 -u hacluster -p secret`         |
| `auth ... addr=<ip>`     | Indica la dirección/puerto de cada host.                   | `pcs host auth node1 addr=10.0.0.1:2224`             |
| `deauth [<hosts>]`       | Elimina la autenticación de los hosts indicados.           | `pcs host deauth node2`                              |

# pcs status
Inspección del estado del clúster, nodos y recursos.

| **Comando / Flag**     | **Descripción**                                          | **Ejemplo**                          |
| ---------------------- | -------------------------------------------------------- | ------------------------------------ |
| `status`               | Estado completo (nodos, recursos, demonios).            | `pcs status`                         |
| `status --full`        | Salida detallada con todos los atributos.               | `pcs status --full`                  |
| `cluster`              | Estado del clúster (equivale a `pcs cluster status`).    | `pcs status cluster`                 |
| `nodes`                | Lista de nodos por estado (online/offline/standby).     | `pcs status nodes`                   |
| `corosync`             | Estado de membresía de Corosync.                        | `pcs status corosync`                |
| `qdevice`              | Estado del dispositivo de quórum.                       | `pcs status qdevice`                 |
| `quorum`               | Estado del quórum.                                      | `pcs status quorum`                  |
| `resources`            | Estado de los recursos configurados.                    | `pcs status resources`               |
| `pcsd [<nodos>]`       | Estado del demonio pcsd.                                | `pcs status pcsd`                    |
| `xml`                  | Vuelca el estado del clúster en XML.                    | `pcs status xml`                     |

# pcs resource
Creación y gestión de recursos del clúster (servicios, IPs, sistemas de ficheros, etc.).

| **Comando / Flag**                       | **Descripción**                                                       | **Ejemplo**                                                                  |
| ---------------------------------------- | --------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `create <id> <tipo> [opts]`              | Crea un recurso a partir de un agente (`ocf:`, `systemd:`, `lsb:`).   | `pcs resource create vip ocf:heartbeat:IPaddr2 ip=10.0.0.10 cidr_netmask=24`|
| `create ... op <op> <attr>`              | Define operaciones (monitor, start, stop) e intervalos.              | `pcs resource create vip ... op monitor interval=30s`                       |
| `create ... meta <attr>`                 | Define metadatos del recurso.                                        | `pcs resource create vip ... meta resource-stickiness=100`                  |
| `create ... --group <g>`                 | Crea el recurso dentro de un grupo.                                 | `pcs resource create vip ... --group webgroup`                              |
| `create ... clone`                       | Crea el recurso como clon (corre en varios nodos).                  | `pcs resource create vip ... clone`                                          |
| `create ... promotable`                  | Crea un recurso promovible (master/slave).                          | `pcs resource create db ocf:... promotable`                                  |
| `create ... --disabled`                  | Crea el recurso parado.                                             | `pcs resource create vip ... --disabled`                                     |
| `list [<filtro>]`                        | Lista agentes de recursos disponibles.                             | `pcs resource list ocf:heartbeat`                                            |
| `describe <agente>`                      | Muestra parámetros y descripción de un agente.                     | `pcs resource describe ocf:heartbeat:IPaddr2`                                |
| `status` / `show`                        | Muestra el estado/configuración de los recursos.                   | `pcs resource status`                                                        |
| `config [<id>]`                          | Muestra la configuración detallada de un recurso.                  | `pcs resource config vip`                                                    |
| `enable <id>`                            | Habilita (arranca) un recurso.                                     | `pcs resource enable vip`                                                    |
| `disable <id>`                           | Deshabilita (para) un recurso.                                     | `pcs resource disable vip`                                                    |
| `restart <id>`                           | Reinicia un recurso.                                               | `pcs resource restart vip`                                                    |
| `move <id> [<nodo>]`                      | Mueve un recurso a otro nodo (crea restricción de ubicación).      | `pcs resource move vip node2`                                                |
| `move ... --master`                      | Mueve la instancia promovida (master) de un recurso.              | `pcs resource move db node2 --master`                                        |
| `ban <id> [<nodo>]`                       | Impide que un recurso corra en un nodo.                            | `pcs resource ban vip node1`                                                  |
| `clear <id> [<nodo>]`                     | Elimina las restricciones creadas por `move`/`ban`.               | `pcs resource clear vip`                                                      |
| `update <id> <attr>`                     | Modifica parámetros de un recurso existente.                      | `pcs resource update vip ip=10.0.0.11`                                       |
| `delete <id>` / `remove <id>`            | Elimina un recurso.                                               | `pcs resource delete vip`                                                     |
| `cleanup [<id>]`                         | Limpia el historial de errores y fuerza re-chequeo.               | `pcs resource cleanup vip`                                                     |
| `refresh [<id>]`                         | Refresca el estado real del recurso desde los nodos.              | `pcs resource refresh vip`                                                     |
| `failcount show <id>`                    | Muestra el contador de fallos de un recurso.                      | `pcs resource failcount show vip`                                            |
| `group add <g> <ids>`                    | Crea/añade recursos a un grupo (orden = arranque secuencial).     | `pcs resource group add webgroup vip web`                                    |
| `group remove <g> <ids>`                 | Saca recursos de un grupo.                                         | `pcs resource group remove webgroup web`                                     |
| `group list`                             | Lista los grupos y sus recursos.                                  | `pcs resource group list`                                                     |
| `clone <id> [opts]`                      | Convierte un recurso existente en clon.                           | `pcs resource clone web clone-max=2`                                          |
| `unclone <id>`                           | Revierte un clon a recurso simple.                               | `pcs resource unclone web`                                                     |
| `promotable <id>`                        | Convierte un recurso en promovible.                              | `pcs resource promotable db`                                                   |
| `manage <id>`                            | Pone el recurso bajo gestión del clúster.                        | `pcs resource manage vip`                                                      |
| `unmanage <id>`                          | Saca el recurso de la gestión del clúster.                       | `pcs resource unmanage vip`                                                    |
| `debug-start <id>`                       | Arranca un recurso manualmente fuera del clúster (diagnóstico).  | `pcs resource debug-start vip`                                                |
| `debug-stop <id>`                        | Para un recurso manualmente (diagnóstico).                       | `pcs resource debug-stop vip`                                                  |
| `debug-monitor <id>`                     | Ejecuta el monitor de un recurso (diagnóstico).                  | `pcs resource debug-monitor vip`                                              |
| `relocate run`                           | Reubica recursos a sus nodos preferidos.                         | `pcs resource relocate run`                                                    |
| `utilization <id> <attr>`                | Define atributos de utilización (capacity-based placement).      | `pcs resource utilization vip cpu=2`                                          |

# pcs resource defaults
Valores por defecto aplicados a todos los recursos.

| **Comando / Flag**          | **Descripción**                                       | **Ejemplo**                                          |
| --------------------------- | ----------------------------------------------------- | ---------------------------------------------------- |
| `defaults`                  | Muestra los valores por defecto de recursos.         | `pcs resource defaults`                              |
| `defaults <attr>=<val>`     | Establece un valor por defecto de meta-atributo.     | `pcs resource defaults resource-stickiness=100`      |
| `op defaults`               | Muestra los valores por defecto de operaciones.      | `pcs resource op defaults`                           |
| `op defaults <attr>=<val>`  | Establece un valor por defecto de operación.         | `pcs resource op defaults timeout=90s`               |

# pcs stonith
Configuración del fencing (STONITH — Shoot The Other Node In The Head) para aislar nodos defectuosos.

| **Comando / Flag**             | **Descripción**                                                    | **Ejemplo**                                                                |
| ------------------------------ | ------------------------------------------------------------------ | -------------------------------------------------------------------------- |
| `list [<filtro>]`              | Lista agentes de fencing disponibles.                             | `pcs stonith list`                                                         |
| `describe <agente>`            | Describe parámetros de un agente de fencing.                      | `pcs stonith describe fence_ipmilan`                                       |
| `create <id> <agente> [opts]`  | Crea un dispositivo de fencing.                                   | `pcs stonith create fence1 fence_ipmilan ip=10.0.0.5 username=admin ...`   |
| `config [<id>]`                | Muestra la configuración de los dispositivos de fencing.         | `pcs stonith config`                                                       |
| `status`                       | Estado de los dispositivos de fencing.                           | `pcs stonith status`                                                       |
| `update <id> <attr>`           | Modifica un dispositivo de fencing.                              | `pcs stonith update fence1 ip=10.0.0.6`                                    |
| `delete <id>` / `remove <id>`  | Elimina un dispositivo de fencing.                               | `pcs stonith delete fence1`                                               |
| `fence <nodo>`                 | Ejecuta fencing manual sobre un nodo.                            | `pcs stonith fence node2`                                                 |
| `confirm <nodo>`               | Confirma manualmente que un nodo ha sido aislado.               | `pcs stonith confirm node2`                                              |
| `cleanup [<id>]`               | Limpia el historial de errores del dispositivo.                | `pcs stonith cleanup fence1`                                              |
| `refresh [<id>]`               | Refresca el estado del dispositivo de fencing.                 | `pcs stonith refresh fence1`                                              |
| `level add <n> <nodo> <dev>`   | Define niveles de fencing topológico (fencing escalonado).     | `pcs stonith level add 1 node2 fence1`                                    |
| `level remove <n>`             | Elimina un nivel de fencing.                                    | `pcs stonith level remove 1`                                              |
| `level clear`                  | Elimina todos los niveles de fencing.                          | `pcs stonith level clear`                                                 |
| `level verify`                 | Verifica la coherencia de los niveles de fencing.              | `pcs stonith level verify`                                                |
| `history show [<nodo>]`        | Muestra el historial de eventos de fencing.                    | `pcs stonith history show`                                                |
| `history cleanup`              | Limpia el historial de fencing.                                | `pcs stonith history cleanup`                                             |

# pcs constraint
Restricciones que determinan dónde, en qué orden y con qué dependencias corren los recursos.

| **Comando / Flag**                        | **Descripción**                                                     | **Ejemplo**                                                            |
| ----------------------------------------- | ------------------------------------------------------------------- | --------------------------------------------------------------------- |
| `config` / `list` / `show`                | Muestra todas las restricciones.                                   | `pcs constraint config`                                               |
| `location <id> prefers <nodo>=<score>`    | Restricción de ubicación: preferencia por un nodo.                | `pcs constraint location vip prefers node1=200`                       |
| `location <id> avoids <nodo>=<score>`     | Restricción de ubicación: evita un nodo.                          | `pcs constraint location vip avoids node2=INFINITY`                   |
| `location add <cid> <rsc> <nodo> <score>` | Añade una restricción de ubicación con ID propio.                | `pcs constraint location add loc1 vip node1 200`                     |
| `colocation add <a> with <b> <score>`     | Coloca dos recursos juntos (o separados con score negativo).     | `pcs constraint colocation add web with vip INFINITY`                |
| `order <a> then <b>`                      | Define orden de arranque (a antes que b).                        | `pcs constraint order vip then web`                                  |
| `order ... kind=<tipo>`                   | Tipo de orden (`Optional`, `Mandatory`, `Serialize`).            | `pcs constraint order vip then web kind=Mandatory`                   |
| `ticket add <rsc> <ticket>`               | Restricción de ticket (clúster multi-sitio con Booth).           | `pcs constraint ticket add web ticketA`                              |
| `remove <id>` / `delete <id>`             | Elimina una restricción por su ID.                               | `pcs constraint remove loc1`                                          |
| `location show` / `colocation show` / `order show` | Muestra restricciones por tipo.                         | `pcs constraint order show`                                          |
| `ref <rsc>`                               | Muestra las restricciones que afectan a un recurso.             | `pcs constraint ref vip`                                              |

# pcs property
Propiedades globales del clúster (CIB).

| **Comando / Flag**             | **Descripción**                                                      | **Ejemplo**                                          |
| ------------------------------ | -------------------------------------------------------------------- | ---------------------------------------------------- |
| `config` / `list` / `show`     | Muestra las propiedades del clúster configuradas.                   | `pcs property config`                                |
| `list --all`                   | Muestra todas las propiedades disponibles con sus valores por defecto.| `pcs property list --all`                            |
| `set <prop>=<val>`             | Establece una propiedad del clúster.                               | `pcs property set stonith-enabled=true`              |
| `set no-quorum-policy=<val>`   | Política ante pérdida de quórum (`stop`, `ignore`, `freeze`, `suicide`). | `pcs property set no-quorum-policy=ignore`     |
| `set maintenance-mode=<bool>`  | Activa/desactiva el modo mantenimiento global.                    | `pcs property set maintenance-mode=true`             |
| `unset <prop>`                 | Elimina una propiedad (vuelve al valor por defecto).              | `pcs property unset maintenance-mode`                |
| `describe <prop>`              | Describe una propiedad concreta.                                 | `pcs property describe stonith-enabled`              |

# pcs node
Gestión del estado y atributos de los nodos.

| **Comando / Flag**                  | **Descripción**                                                | **Ejemplo**                                          |
| ----------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------- |
| `standby <nodo>`                    | Pone un nodo en standby (no aloja recursos).                  | `pcs node standby node1`                            |
| `standby --all`                     | Pone todos los nodos en standby.                             | `pcs node standby --all`                            |
| `unstandby <nodo>`                  | Saca un nodo del standby.                                     | `pcs node unstandby node1`                          |
| `maintenance <nodo>`                | Pone un nodo en modo mantenimiento (recursos sin gestión).   | `pcs node maintenance node1`                        |
| `unmaintenance <nodo>`              | Saca un nodo del modo mantenimiento.                        | `pcs node unmaintenance node1`                      |
| `attribute <nodo> <attr>=<val>`     | Establece un atributo de nodo (útil en reglas de location).  | `pcs node attribute node1 rack=A`                   |
| `attribute --name <attr>`           | Muestra un atributo concreto en todos los nodos.            | `pcs node attribute --name rack`                    |
| `utilization <nodo> <attr>=<val>`   | Define capacidad del nodo (placement basado en utilización). | `pcs node utilization node1 cpu=4 memory=8192`      |

# pcs quorum
Configuración del quórum del clúster.

| **Comando / Flag**            | **Descripción**                                              | **Ejemplo**                                          |
| ----------------------------- | ------------------------------------------------------------ | ---------------------------------------------------- |
| `config`                      | Muestra la configuración del quórum.                        | `pcs quorum config`                                  |
| `status`                      | Estado actual del quórum.                                   | `pcs quorum status`                                  |
| `expected-votes <n>`          | Fija el número esperado de votos (temporal).               | `pcs quorum expected-votes 2`                        |
| `update <attr>=<val>`         | Modifica opciones de quórum (`auto_tie_breaker`, `wait_for_all`, `last_man_standing`). | `pcs quorum update wait_for_all=1`     |
| `unblock`                     | Desbloquea el quórum cuando hay nodos esperando (recuperación). | `pcs quorum unblock`                            |
| `device add <opts>`           | Añade un dispositivo de quórum (qdevice) al clúster.       | `pcs quorum device add model net host=qnode algorithm=ffsplit` |
| `device update <opts>`        | Modifica el dispositivo de quórum.                        | `pcs quorum device update model host=qnode2`         |
| `device remove`               | Elimina el dispositivo de quórum.                         | `pcs quorum device remove`                           |
| `device status`               | Estado del dispositivo de quórum.                         | `pcs quorum device status`                           |

# pcs qdevice
Gestión del demonio de quórum (`corosync-qnetd`) en el nodo árbitro.

| **Comando / Flag**            | **Descripción**                                          | **Ejemplo**                                          |
| ----------------------------- | -------------------------------------------------------- | ---------------------------------------------------- |
| `status <modelo>`             | Estado del qnetd (modelo `net`).                        | `pcs qdevice status net`                             |
| `setup model net`             | Inicializa el qnetd en el nodo árbitro.                | `pcs qdevice setup model net --enable --start`       |
| `start <modelo>`              | Arranca el qnetd.                                      | `pcs qdevice start net`                              |
| `stop <modelo>`               | Detiene el qnetd.                                      | `pcs qdevice stop net`                               |
| `enable <modelo>`             | Habilita el qnetd al inicio.                          | `pcs qdevice enable net`                             |
| `disable <modelo>`            | Deshabilita el qnetd al inicio.                       | `pcs qdevice disable net`                            |
| `destroy <modelo>`            | Destruye la configuración del qnetd.                  | `pcs qdevice destroy net`                            |

# pcs acl
Listas de control de acceso (permisos de usuarios/roles sobre el CIB).

| **Comando / Flag**                     | **Descripción**                                       | **Ejemplo**                                          |
| -------------------------------------- | ----------------------------------------------------- | ---------------------------------------------------- |
| `enable`                               | Habilita las ACL en el clúster.                      | `pcs acl enable`                                     |
| `disable`                              | Deshabilita las ACL.                                 | `pcs acl disable`                                    |
| `config` / `show`                      | Muestra la configuración de ACL.                     | `pcs acl config`                                     |
| `role create <rol> [perms]`            | Crea un rol con permisos.                            | `pcs acl role create readonly read xpath /cib`       |
| `role delete <rol>`                    | Elimina un rol.                                      | `pcs acl role delete readonly`                       |
| `role assign <rol> <usuario>`          | Asigna un rol a un usuario.                          | `pcs acl role assign readonly user juan`             |
| `role unassign <rol> <usuario>`        | Quita un rol a un usuario.                           | `pcs acl role unassign readonly user juan`           |
| `user create <usuario> <roles>`        | Crea un usuario ACL y le asigna roles.              | `pcs acl user create juan readonly`                  |
| `user delete <usuario>`                | Elimina un usuario ACL.                             | `pcs acl user delete juan`                           |
| `group create <grupo> <roles>`         | Crea un grupo ACL con roles.                        | `pcs acl group create ops readonly`                  |
| `permission add <rol> <perm>`          | Añade permisos a un rol.                            | `pcs acl permission add readonly read xpath /cib`    |
| `permission delete <id>`               | Elimina un permiso.                                | `pcs acl permission delete perm-id`                  |

# pcs alert
Configuración de alertas (scripts ejecutados ante eventos del clúster).

| **Comando / Flag**                  | **Descripción**                                        | **Ejemplo**                                                       |
| ----------------------------------- | ------------------------------------------------------ | ----------------------------------------------------------------- |
| `create path=<script> [id=]`        | Crea una alerta con su agente/script.                 | `pcs alert create path=/usr/share/pacemaker/alerts/alert_smtp.sh id=mail` |
| `config` / `show`                   | Muestra las alertas configuradas.                     | `pcs alert config`                                                |
| `update <id> [opts]`                | Modifica una alerta.                                  | `pcs alert update mail options email=admin@x.io`                  |
| `remove <id>`                       | Elimina una alerta.                                  | `pcs alert remove mail`                                            |
| `recipient add <id> value=<dest>`   | Añade un destinatario a una alerta.                  | `pcs alert recipient add mail value=admin@x.io`                   |
| `recipient remove <rid>`            | Elimina un destinatario.                             | `pcs alert recipient remove mail-recipient`                       |

# pcs config
Vista, backup y restauración de la configuración completa del clúster.

| **Comando / Flag**             | **Descripción**                                          | **Ejemplo**                                          |
| ------------------------------ | -------------------------------------------------------- | ---------------------------------------------------- |
| `config`                       | Muestra toda la configuración del clúster.              | `pcs config`                                         |
| `checkpoint`                   | Lista checkpoints de configuración guardados.          | `pcs config checkpoint`                              |
| `checkpoint view <n>`          | Muestra un checkpoint concreto.                        | `pcs config checkpoint view 3`                       |
| `checkpoint restore <n>`       | Restaura la configuración a un checkpoint.            | `pcs config checkpoint restore 3`                    |
| `backup <fichero>`             | Crea un backup (tar) de la configuración.             | `pcs config backup cluster.tar.bz2`                  |
| `restore <fichero>`            | Restaura un backup de configuración.                 | `pcs config restore cluster.tar.bz2`                 |

# pcs booth
Soporte para clústeres multi-sitio con el árbitro Booth (gestión de tickets).

| **Comando / Flag**                | **Descripción**                                       | **Ejemplo**                                          |
| --------------------------------- | ----------------------------------------------------- | ---------------------------------------------------- |
| `setup sites <s1> <s2> arbitrators <a>` | Configura una instancia Booth con sitios y árbitros. | `pcs booth setup sites 10.0.0.1 10.0.0.2 arbitrators 10.0.0.3` |
| `ticket add <ticket>`             | Añade un ticket a la configuración Booth.            | `pcs booth ticket add ticketA`                       |
| `ticket remove <ticket>`          | Elimina un ticket.                                  | `pcs booth ticket remove ticketA`                    |
| `ticket grant <ticket> [<sitio>]` | Concede un ticket a un sitio.                       | `pcs booth ticket grant ticketA`                     |
| `ticket revoke <ticket> [<sitio>]`| Revoca un ticket de un sitio.                      | `pcs booth ticket revoke ticketA`                    |
| `create ip <addr>`                | Crea el recurso Booth en el clúster.               | `pcs booth create ip 10.0.0.10`                      |
| `remove`                          | Elimina el recurso Booth.                          | `pcs booth remove`                                   |
| `status`                          | Estado de Booth.                                   | `pcs booth status`                                   |
| `sync`                            | Sincroniza la configuración Booth a los nodos.     | `pcs booth sync`                                     |

# pcs tag
Etiquetas para agrupar objetos del CIB y operar sobre ellos en bloque.

| **Comando / Flag**          | **Descripción**                                  | **Ejemplo**                                          |
| --------------------------- | ------------------------------------------------ | ---------------------------------------------------- |
| `create <tag> <ids>`        | Crea una etiqueta con los objetos indicados.    | `pcs tag create web-stack vip web`                  |
| `config [<tag>]`            | Muestra las etiquetas definidas.               | `pcs tag config`                                    |
| `list`                      | Lista las etiquetas.                           | `pcs tag list`                                      |
| `update <tag> add <ids>`    | Añade objetos a una etiqueta.                  | `pcs tag update web-stack add db`                   |
| `update <tag> remove <ids>` | Quita objetos de una etiqueta.                | `pcs tag update web-stack remove db`                |
| `delete <tag>` / `remove`   | Elimina una etiqueta.                          | `pcs tag delete web-stack`                          |

# pcs dr
Disaster Recovery: relación entre un clúster primario y uno de recuperación.

| **Comando / Flag**          | **Descripción**                                              | **Ejemplo**                                          |
| --------------------------- | ------------------------------------------------------------ | ---------------------------------------------------- |
| `config`                    | Muestra la configuración de disaster recovery.              | `pcs dr config`                                      |
| `status`                    | Estado de ambos clústeres (primario y recovery).           | `pcs dr status`                                      |
| `set-recovery-site <nodo>`  | Define el clúster de recuperación.                         | `pcs dr set-recovery-site node-dr1`                  |
| `destroy`                   | Elimina la configuración de disaster recovery.            | `pcs dr destroy`                                     |

# pcs pcsd
Gestión del demonio pcsd y sus certificados.

| **Comando / Flag**             | **Descripción**                                       | **Ejemplo**                                          |
| ------------------------------ | ----------------------------------------------------- | ---------------------------------------------------- |
| `certkey <cert> <key>`         | Establece el certificado y clave SSL de pcsd.        | `pcs pcsd certkey /path/cert.pem /path/key.pem`      |
| `sync-certificates`            | Sincroniza los certificados pcsd entre nodos.        | `pcs pcsd sync-certificates`                         |
| `status [<nodos>]`             | Estado del demonio pcsd en los nodos.               | `pcs pcsd status node1 node2`                        |
| `accept_token <fichero>`       | Acepta un token de autenticación.                   | `pcs pcsd accept_token /path/token`                  |

# pcs client
Configuración local del cliente pcs.

| **Comando / Flag**     | **Descripción**                                          | **Ejemplo**                                          |
| ---------------------- | -------------------------------------------------------- | ---------------------------------------------------- |
| `local-auth [<puerto>]`| Autentica el cliente local contra el pcsd local.        | `pcs client local-auth`                              |
