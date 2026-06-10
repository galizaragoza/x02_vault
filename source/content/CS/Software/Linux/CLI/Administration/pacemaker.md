**Pacemaker** es el gestor de recursos de clúster (CRM) de referencia en Linux para alta disponibilidad. Coordina el arranque, parada, monitorización y recuperación de servicios (recursos) sobre una capa de membresía/mensajería —normalmente **Corosync**— aplicando restricciones de colocación, orden y ubicación, y aislando nodos defectuosos mediante *fencing* (STONITH). Su estado completo vive en la **CIB** (Cluster Information Base), un documento XML replicado entre nodos. Esta guía cubre el demonio `pacemakerd` y la familia nativa de herramientas de línea de comandos (`crm_*`, `cibadmin`, `stonith_admin`); para la administración de alto nivel ver [[pcs]] (front-end de Red Hat/SUSE) y `crmsh`.

# Arquitectura y demonios
Pacemaker arranca como un conjunto de procesos hijos de `pacemakerd`. Conocerlos ayuda a interpretar logs y a qué herramienta habla con cada uno.

| **Componente**   | **Descripción**                                                                                  |
| ---------------- | ------------------------------------------------------------------------------------------------ |
| `pacemakerd`     | Demonio maestro; lanza y supervisa el resto de subdemonios.                                       |
| `pacemaker-controld` | Controlador (CRMd); coordina transiciones y elige el DC (Designated Coordinator).             |
| `pacemaker-schedulerd` | Planificador (PEngine); calcula el estado objetivo del clúster a partir de la CIB.           |
| `pacemaker-based`| Gestiona la CIB (Cluster Information Base) y su replicación.                                       |
| `pacemaker-execd`| Ejecutor local de agentes de recurso (LRMd).                                                      |
| `pacemaker-fenced` | Demonio de fencing/STONITH.                                                                      |
| `pacemaker-attrd`| Gestiona los atributos transitorios de nodo.                                                      |

# pacemakerd
Demonio principal. Normalmente se gestiona vía `systemctl` (ver [[systemctl]]); se invoca directamente sobre todo para consultar features o solicitar parada ordenada.

| **Flag**                 | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------ | ------------------------------------------------------------------------ | ------------------------------------ |
| `-h`, `--help`           | Muestra la ayuda.                                                        | `pacemakerd --help`                  |
| `-$`, `--version`        | Muestra la versión.                                                      | `pacemakerd --version`               |
| `-F`, `--features`       | Lista las características compiladas (corosync, acls, systemd, etc.).      | `pacemakerd --features`              |
| `-f`, `--foreground`     | Ejecuta en primer plano (no se demoniza); útil para depuración.           | `pacemakerd -f`                      |
| `-p`, `--pid-file=FILE`  | Ruta del fichero PID.                                                     | `pacemakerd -p /run/pacemaker.pid`   |
| `-S`, `--shutdown`       | Solicita la parada ordenada del clúster local.                            | `pacemakerd --shutdown`              |
| `-V`, `--verbose`        | Aumenta el nivel de detalle (repetible).                                  | `pacemakerd -VV`                     |

# crm_mon
Monitor del estado del clúster: nodos, recursos, fallos y operaciones. Por defecto refresca en pantalla; admite salida única para scripts.

| **Flag**                       | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------------ | ------------------------------------------------------------------------ | ------------------------------------ |
| `-1`, `--one-shot`             | Muestra el estado una vez y sale (ideal para scripts/cron).               | `crm_mon -1`                         |
| `-d`, `--daemonize`            | Ejecuta como demonio en segundo plano.                                    | `crm_mon -d`                         |
| `-i`, `--interval=TIMESPEC`    | Intervalo de refresco (p. ej. `2s`, `500ms`).                            | `crm_mon -i 5s`                      |
| `-p`, `--pid-file=FILE`        | Fichero PID al usar `-d`.                                                 | `crm_mon -d -p /run/crm_mon.pid`     |
| `-n`, `--group-by-node`        | Agrupa los recursos por nodo.                                            | `crm_mon -1 -n`                      |
| `-r`, `--inactive`            | Muestra también los recursos inactivos.                                  | `crm_mon -1 -r`                      |
| `-R`, `--show-detail`          | Muestra detalles adicionales (IDs internos, scores).                     | `crm_mon -1 -R`                      |
| `-b`, `--brief`                | Salida resumida.                                                         | `crm_mon -1 -b`                      |
| `-f`, `--failcounts`           | Muestra contadores de fallo de los recursos.                             | `crm_mon -1 -f`                      |
| `-o`, `--operations`           | Muestra las operaciones de los recursos.                                 | `crm_mon -1 -o`                      |
| `-t`, `--timing-details`       | Muestra tiempos de las operaciones.                                      | `crm_mon -1 -t`                      |
| `-A`, `--show-node-attributes` | Muestra los atributos de nodo.                                           | `crm_mon -1 -A`                      |
| `-c`, `--tickets`              | Muestra el estado de los tickets (multi-site/booth).                     | `crm_mon -1 -c`                      |
| `-D`, `--hide-headers`         | Oculta las cabeceras de la salida.                                       | `crm_mon -1 -D`                      |
| `-e`, `--external-agent=FILE`  | Programa a ejecutar ante cambios de estado.                              | `crm_mon -d -e /usr/local/bin/notify`|
| `-E`, `--external-recipient=R` | Destinatario pasado al agente externo.                                   | `crm_mon -d -e notify -E admin@x`    |
| `--include=SECTIONS`           | Incluye secciones concretas en la salida (`all`, `nodes`, `resources`...).| `crm_mon -1 --include=nodes,resources`|
| `--exclude=SECTIONS`           | Excluye secciones de la salida.                                          | `crm_mon -1 --exclude=options`       |
| `--output-as=FORMAT`           | Formato de salida (`text`, `html`, `xml`, `console`).                    | `crm_mon -1 --output-as=xml`         |
| `--output-to=DEST`             | Destino de la salida (fichero o `-` para stdout).                        | `crm_mon -1 --output-as=html --output-to=/var/www/cluster.html` |
| `-V`, `--verbose`              | Aumenta el detalle (repetible).                                          | `crm_mon -1 -VV`                     |
| `-Q`, `--quiet`                | Suprime salida no esencial.                                              | `crm_mon -1 -Q`                      |

> Las antiguas `-X/--as-xml`, `-h/--as-html`, `-w/--web-cgi` y `-s/--simple-status` están obsoletas; usar `--output-as`/`--output-to`.

# cibadmin
Manipulación de bajo nivel de la CIB (XML). Potente y peligrosa: opera directamente sobre la base replicada. Las opciones de comando son mutuamente excluyentes.

## Comandos (acción)
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-Q`, `--query`           | Consulta (vuelca) la CIB o una sección.                                  | `cibadmin --query`                   |
| `-C`, `--create`          | Crea un objeto nuevo a partir de XML.                                    | `cibadmin -C -o resources -x res.xml`|
| `-R`, `--replace`         | Reemplaza una sección/objeto por el XML dado.                            | `cibadmin -R -o configuration -x c.xml`|
| `-U`, `--update`          | Actualiza (modifica) un objeto existente.                                | `cibadmin -U -x patch.xml`           |
| `-M`, `--modify`          | Modifica atributos según XPath/XML.                                      | `cibadmin --modify -x mod.xml`       |
| `-D`, `--delete`          | Elimina el objeto que coincide con el XML/selector.                      | `cibadmin -D -o resources -x res.xml`|
| `-d`, `--delete-all`      | Elimina todos los objetos que casen con el XPath (`-A`).                  | `cibadmin --delete-all -A "//primitive[@class='ocf']"` |
| `-P`, `--patch`           | Aplica un parche (diff) a la CIB.                                         | `cibadmin --patch -x diff.xml`       |
| `-E`, `--erase`           | Borra el contenido de la CIB (¡destructivo!).                            | `cibadmin --erase`                   |
| `-B`, `--bump`            | Incrementa el número de epoch de la CIB.                                 | `cibadmin --bump`                    |
| `-a`, `--empty`           | Genera una plantilla de CIB vacía.                                       | `cibadmin --empty`                   |
| `--upgrade`               | Actualiza el esquema de la CIB a la última versión.                      | `cibadmin --upgrade --force`         |
| `-5`, `--md5-sum`         | Calcula el hash MD5 del contenido.                                       | `cibadmin -5 -x cib.xml`             |
| `--md5-sum-versioned`     | MD5 incluyendo los campos de versión.                                    | `cibadmin --md5-sum-versioned`       |
| `--show-access[=MODE]`    | Muestra el acceso ACL efectivo del usuario.                              | `cibadmin --show-access`             |

## Selección y modificadores
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-o`, `--scope=SECTION`   | Limita a una sección (`configuration`, `resources`, `constraints`, `status`, `nodes`, `crm_config`). | `cibadmin -Q -o resources` |
| `-A`, `--xpath=QUERY`     | Selecciona objetos mediante XPath.                                       | `cibadmin -Q -A "//node"`            |
| `-e`, `--xpath-no-children`| Como `-A` pero sin incluir los hijos.                                   | `cibadmin -Q -e -A "//primitive"`    |
| `-x`, `--xml-file=FILE`   | Lee el XML de entrada de un fichero.                                     | `cibadmin -C -o resources -x r.xml`  |
| `-X`, `--xml-text=STRING` | XML de entrada como cadena en línea de comandos.                         | `cibadmin -M -X '<...>'`             |
| `-p`, `--xml-pipe`        | Lee el XML de entrada de stdin.                                          | `cat r.xml \| cibadmin -C -o resources -p` |
| `-t`, `--timeout=SEC`     | Timeout de la operación.                                                 | `cibadmin -Q -t 30`                  |
| `-f`, `--force`           | Fuerza la operación.                                                     | `cibadmin --erase --force`           |
| `-s`, `--sync-call`       | Espera a que la operación complete (llamada síncrona).                   | `cibadmin -C -s ...`                 |
| `-l`, `--local`           | Opera solo sobre la copia local de la CIB.                               | `cibadmin -Q -l`                     |
| `-n`, `--no-children`     | No incluye los nodos hijos en la salida.                                 | `cibadmin -Q -n`                     |
| `-V`, `--verbose`         | Aumenta el detalle.                                                      | `cibadmin -Q -V`                     |

# crm_resource
Gestión integral de recursos: consulta, parámetros, ubicación, limpieza de fallos y operaciones forzadas.

## Selección de recurso (común)
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-r`, `--resource=ID`     | Recurso objetivo.                                                        | `crm_resource -r webip -W`           |
| `-N`, `--node=NAME`       | Nodo objetivo de la operación.                                           | `crm_resource -r web -M -N node2`    |
| `-n`, `--operation=OP`    | Operación afectada (para failcounts/forzadas).                           | `crm_resource -r web -n monitor`     |
| `-i`, `--interval=SPEC`   | Intervalo de la operación.                                               | `crm_resource -r web -n monitor -i 10s` |
| `-t`, `--resource-type=T` | Tipo de recurso (al crear/consultar).                                    | `crm_resource -t primitive`          |
| `-u`, `--lifetime=PERIOD` | Duración de una restricción (p. ej. de un `--ban`/`--move`).             | `crm_resource -B -r web -u PT1H`     |

## Consultas
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-L`, `--list`            | Lista todos los recursos.                                                | `crm_resource -L`                    |
| `-l`, `--list-raw`        | Lista los IDs de recurso en crudo.                                       | `crm_resource -l`                    |
| `-O`, `--list-operations` | Lista operaciones activas/recientes.                                     | `crm_resource -O`                    |
| `-o`, `--list-all-operations` | Lista todas las operaciones (incluidas históricas).                  | `crm_resource -o`                    |
| `-W`, `--locate`          | Indica en qué nodo(s) corre el recurso.                                  | `crm_resource -r web -W`             |
| `-Q`, `--query-xml`       | Vuelca el XML del recurso.                                               | `crm_resource -r web -Q`             |
| `-A`, `--constraints`     | Muestra las restricciones que afectan al recurso.                        | `crm_resource -r web -A`             |
| `--why`                   | Explica por qué un recurso está (o no) corriendo.                        | `crm_resource -r web --why`          |

## Parámetros y propiedades
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-g`, `--get-parameter=N` | Lee un parámetro de instancia del recurso.                               | `crm_resource -r web -g ip`          |
| `-p`, `--set-parameter=N` | Fija un parámetro de instancia (con `-v`).                               | `crm_resource -r web -p ip -v 10.0.0.5` |
| `-d`, `--delete-parameter=N` | Borra un parámetro de instancia.                                      | `crm_resource -r web -d ip`          |
| `-v`, `--parameter-value=V`| Valor para `-p`/`-S`.                                                   | `crm_resource -r web -p ip -v 10.0.0.5` |
| `-G`, `--get-property=N`  | Lee una propiedad de definición del recurso.                             | `crm_resource -r web -G is-managed`  |
| `-S`, `--set-property=N`  | Fija una propiedad de definición.                                        | `crm_resource -r web -S is-managed -v false` |
| `-m`, `--meta`            | Indica que `-p`/`-g`/`-d` operan sobre meta-atributos.                    | `crm_resource -r web -m -p target-role -v Stopped` |
| `-u`, `--utilization`     | Opera sobre atributos de utilización.                                    | `crm_resource -r web -u -p cpu -v 2` |
| `-s`, `--set-name=NAME`   | Nombre del bloque de atributos sobre el que actuar.                       | `crm_resource -r web -p ip -v X -s web-params` |

## Acciones de ubicación y ciclo de vida
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-M`, `--move`            | Mueve el recurso a otro nodo (crea restricción de ubicación).            | `crm_resource -r web -M -N node2`    |
| `-B`, `--ban`             | Prohíbe que el recurso corra en un nodo.                                 | `crm_resource -r web -B -N node1`    |
| `-U`, `--clear`           | Elimina las restricciones creadas por `-M`/`-B`.                         | `crm_resource -r web -U`             |
| `--restart`               | Reinicia el recurso (stop+start coordinado).                            | `crm_resource -r web --restart`      |
| `--wait`                  | Espera a que el clúster alcance el estado estable.                       | `crm_resource --wait`                |
| `-C`, `--cleanup`         | Limpia el historial/fallos del recurso y reprueba.                       | `crm_resource -r web -C`             |
| `-R`, `--refresh`         | Reexamina el estado real del recurso, ignorando el historial.            | `crm_resource -r web -R`             |
| `-P`, `--reprobe`         | Vuelve a sondear el estado de todos los recursos.                        | `crm_resource -P`                    |
| `-F`, `--fail`            | Marca el recurso como fallido en un nodo (provoca recuperación).         | `crm_resource -r web -F -N node1`    |
| `--validate`             | Ejecuta la acción `validate-all` del agente sin afectar al clúster.      | `crm_resource -r web --validate`     |
| `--force-start`           | Arranca el recurso directamente, ignorando el clúster (depuración).      | `crm_resource -r web --force-start`  |
| `--force-stop`            | Para el recurso directamente.                                            | `crm_resource -r web --force-stop`   |
| `--force-check`           | Comprueba el estado directamente.                                        | `crm_resource -r web --force-check`  |
| `--force-promote`         | Promociona (modo promoted) directamente.                                 | `crm_resource -r web --force-promote`|
| `--force-demote`          | Degrada directamente.                                                    | `crm_resource -r web --force-demote` |
| `-f`, `--force`           | Fuerza acciones potencialmente peligrosas.                               | `crm_resource -r web -C -f`          |
| `-V`, `--verbose`         | Aumenta el detalle.                                                      | `crm_resource -L -V`                 |

# crm_attribute
Lee y modifica atributos de nodo y propiedades de configuración del clúster (escribe en distintas secciones de la CIB según `--type`).

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-n`, `--name=NAME`       | Nombre del atributo/propiedad.                                           | `crm_attribute -n maintenance-mode`  |
| `-v`, `--update=VALUE`    | Fija el valor del atributo.                                              | `crm_attribute -n maintenance-mode -v true` |
| `-G`, `--query`           | Consulta el valor actual.                                                | `crm_attribute -n maintenance-mode -G`|
| `-D`, `--delete`          | Elimina el atributo.                                                     | `crm_attribute -n stonith-enabled -D`|
| `-t`, `--type=SECTION`    | Sección destino: `crm_config`, `nodes`, `status`, `reboot`, `forever`.   | `crm_attribute -t nodes -N node1 -n rack -v A` |
| `-N`, `--node=NAME`       | Nodo objetivo (para atributos de nodo).                                  | `crm_attribute -N node1 -n standby -v on` |
| `-l`, `--lifetime=LIFE`   | Duración del atributo de nodo: `reboot` (transitorio) o `forever`.       | `crm_attribute -N node1 -l reboot -n standby -v on` |
| `-i`, `--id=ID`           | ID XML explícito del atributo.                                           | `crm_attribute -i nvpair-1 -G`       |
| `-s`, `--set-name=NAME`   | Nombre del conjunto (nvset) que contiene el atributo.                     | `crm_attribute -s node1-attrs -n rack -G` |
| `-p`, `--promotion[=RSC]` | Atributo de score de promoción (sustituye al antiguo `crm_master`).      | `crm_attribute -p -v 100`            |
| `-d`, `--default=VALUE`   | Valor devuelto si el atributo no existe.                                 | `crm_attribute -n foo -G -d 0`       |
| `-q`, `--quiet`           | Imprime solo el valor en consultas.                                      | `crm_attribute -n foo -G -q`         |

# crm_node
Información de membresía del clúster y eliminación de nodos.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-n`, `--name`            | Muestra el nombre del nodo local.                                        | `crm_node -n`                        |
| `-i`, `--cluster-id`      | Muestra el ID del nodo local.                                            | `crm_node -i`                        |
| `-l`, `--list`            | Lista los nodos conocidos y su ID.                                       | `crm_node -l`                        |
| `-p`, `--partition`       | Lista los nodos de la partición actual.                                  | `crm_node -p`                        |
| `-q`, `--quorum`          | Indica si la partición tiene quórum (1/0).                               | `crm_node -q`                        |
| `-R`, `--remove=NAME`     | Elimina un nodo de la CIB y del clúster.                                 | `crm_node --remove node3`            |
| `--name-for-id=ID`        | Devuelve el nombre asociado a un ID de nodo.                             | `crm_node --name-for-id 2`           |
| `-f`, `--force`           | Fuerza la operación.                                                     | `crm_node --remove node3 -f`         |

# crm_simulate
Simula el comportamiento del planificador (*what-if*) sobre una CIB real o de fichero, sin tocar el clúster. Base del análisis de transiciones y de la depuración de políticas.

## Entrada
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-L`, `--live-check`      | Usa la CIB en vivo del clúster.                                          | `crm_simulate -L -S`                 |
| `-x`, `--xml-file=FILE`   | Usa una CIB guardada en fichero.                                         | `crm_simulate -x cib.xml -S`         |
| `-Q`, `--quiet`           | Salida mínima.                                                           | `crm_simulate -L -S -Q`              |

## Acción
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-S`, `--simulate`        | Simula la transición y muestra las acciones resultantes.                 | `crm_simulate -L -S`                 |
| `-R`, `--run`             | Calcula la transición sin ejecutar acciones (solo estado objetivo).      | `crm_simulate -L -R`                 |
| `-s`, `--show-scores`     | Muestra los scores de asignación de recursos.                            | `crm_simulate -L -s`                 |
| `-U`, `--show-utilization`| Muestra el uso de utilización por nodo.                                  | `crm_simulate -L -U`                 |
| `-p`, `--show-pending`    | Muestra acciones pendientes.                                             | `crm_simulate -L -S -p`              |
| `-a`, `--all-actions`     | Muestra todas las acciones, incluso las omitidas.                        | `crm_simulate -L -S -a`              |

## Inyección de eventos (escenarios)
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-u`, `--node-up=NODE`    | Simula un nodo que se incorpora.                                         | `crm_simulate -x cib.xml -S -u node2`|
| `-d`, `--node-down=NODE`  | Simula un nodo que se va.                                                | `crm_simulate -x cib.xml -S -d node1`|
| `-f`, `--node-fail=NODE`  | Simula el fallo de un nodo.                                              | `crm_simulate -x cib.xml -S -f node1`|
| `-i`, `--op-inject=SPEC`  | Inyecta el resultado de una operación de recurso.                        | `crm_simulate -x cib.xml -S -i web_monitor_0@node1=7` |
| `-F`, `--op-fail=SPEC`    | Inyecta el fallo de una operación.                                       | `crm_simulate -x cib.xml -S -F web_start_0@node1=1`   |
| `-t`, `--ticket-grant=ID` | Simula la concesión de un ticket.                                        | `crm_simulate -x cib.xml -S -t tk1`  |
| `-T`, `--ticket-revoke=ID`| Simula la revocación de un ticket.                                       | `crm_simulate -x cib.xml -S -T tk1`  |
| `-q`, `--quorum=q\|nq`    | Fuerza el estado de quórum de la partición.                              | `crm_simulate -x cib.xml -S -q nq`   |
| `-w`, `--watchdog=b`      | Fuerza la disponibilidad de watchdog (SBD).                             | `crm_simulate -x cib.xml -S -w true` |
| `-m`, `--set-datetime=T`  | Fija la fecha/hora simulada (reglas time-based).                         | `crm_simulate -x cib.xml -S -m "2026-06-09 02:00"` |

## Salida de artefactos
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-I`, `--save-input=FILE` | Guarda la CIB de entrada usada.                                          | `crm_simulate -L -S -I in.xml`       |
| `-O`, `--save-output=FILE`| Guarda la CIB resultante de la transición.                               | `crm_simulate -x cib.xml -S -O out.xml` |
| `-G`, `--save-graph=FILE` | Guarda el grafo de transición (XML).                                     | `crm_simulate -x cib.xml -S -G g.xml`|
| `-D`, `--save-dotfile=FILE`| Guarda el grafo en formato Graphviz `.dot`.                            | `crm_simulate -x cib.xml -S -D g.dot`|
| `-i`, `--in-place`        | Reescribe la CIB de entrada con el resultado.                            | `crm_simulate -x cib.xml -S -i`      |

# crm_verify
Valida la sintaxis y consistencia de una configuración CIB antes de aplicarla.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-L`, `--live-check`      | Verifica la CIB en vivo.                                                 | `crm_verify -L`                      |
| `-x`, `--xml-file=FILE`   | Verifica una CIB de fichero.                                             | `crm_verify -x cib.xml`              |
| `-X`, `--xml-text=STRING` | Verifica XML pasado como cadena.                                         | `crm_verify -X '<cib>...'`           |
| `-p`, `--xml-pipe`        | Verifica XML leído de stdin.                                             | `cat cib.xml \| crm_verify -p`       |
| `-V`, `--verbose`         | Muestra detalles de los errores/avisos.                                  | `crm_verify -L -V`                   |
| `-Q`, `--quiet`           | Solo código de salida, sin mensajes.                                     | `crm_verify -L -Q`                   |

# crm_diff
Calcula y aplica diferencias entre dos CIBs (usado en parches y backups).

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-o`, `--original=FILE`   | CIB original (fichero).                                                  | `crm_diff -o old.xml -n new.xml`     |
| `-n`, `--new=FILE`        | CIB nueva (fichero).                                                     | `crm_diff -o old.xml -n new.xml`     |
| `-O`, `--original-string=S`| CIB original como cadena.                                               | `crm_diff -O '<cib>...' -N '<cib>...'`|
| `-N`, `--new-string=S`    | CIB nueva como cadena.                                                   | `crm_diff -O '...' -N '...'`         |
| `-c`, `--cib`             | Trata la entrada como CIB y produce un diff versionado aplicable.        | `crm_diff -c -o old.xml -n new.xml`  |
| `-V`, `--verbose`         | Aumenta el detalle.                                                      | `crm_diff -o old.xml -n new.xml -V`  |

# crm_shadow
Crea un "sandbox" (copia shadow) de la CIB para editar y validar cambios antes de aplicarlos al clúster real.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-c`, `--create=NAME`     | Crea una shadow CIB copiando la activa.                                  | `crm_shadow --create test`           |
| `-e`, `--create-empty=NAME`| Crea una shadow CIB vacía.                                              | `crm_shadow --create-empty test`     |
| `-w`, `--which`           | Muestra la shadow CIB en uso actualmente.                                | `crm_shadow --which`                 |
| `-p`, `--display`         | Muestra el contenido de la shadow CIB.                                   | `crm_shadow --display`               |
| `-D`, `--diff`            | Muestra las diferencias respecto a la CIB activa.                        | `crm_shadow --diff`                  |
| `-E`, `--edit`            | Edita la shadow CIB en `$EDITOR`.                                        | `crm_shadow --edit`                  |
| `-C`, `--commit=NAME`     | Aplica la shadow CIB al clúster real.                                    | `crm_shadow --commit test`           |
| `-r`, `--reset=NAME`      | Restablece la shadow a partir de la CIB activa.                          | `crm_shadow --reset test`            |
| `-d`, `--delete=NAME`     | Elimina la shadow CIB.                                                   | `crm_shadow --delete test`           |
| `-s`, `--switch=NAME`     | Cambia a otra shadow CIB.                                                | `crm_shadow --switch test`           |
| `-b`, `--batch`           | Modo no interactivo (no abre subshell).                                  | `crm_shadow --create test -b`        |
| `-F`, `--force`           | Fuerza la operación.                                                     | `crm_shadow --commit test -F`        |

# crm_ticket
Gestión de tickets para clústeres multi-site (booth): conceder, revocar y consultar.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-t`, `--ticket=ID`       | Ticket objetivo.                                                         | `crm_ticket -t ticketA -G granted`   |
| `-g`, `--grant`           | Concede el ticket localmente.                                            | `crm_ticket -t ticketA -g`           |
| `-r`, `--revoke`          | Revoca el ticket.                                                        | `crm_ticket -t ticketA -r`           |
| `-s`, `--standby`         | Pone el ticket en standby.                                               | `crm_ticket -t ticketA -s`           |
| `-a`, `--activate`        | Reactiva un ticket en standby.                                           | `crm_ticket -t ticketA -a`           |
| `-G`, `--get-attr=ATTR`   | Consulta un atributo del ticket.                                         | `crm_ticket -t ticketA -G granted`   |
| `-S`, `--set-attr=ATTR`   | Fija un atributo del ticket (con `-v`).                                  | `crm_ticket -t ticketA -S owner -v site1` |
| `-D`, `--delete-attr=ATTR`| Borra un atributo del ticket.                                           | `crm_ticket -t ticketA -D owner`     |
| `-v`, `--attr-value=VALUE`| Valor para `-S`.                                                        | `crm_ticket -t ticketA -S owner -v site1` |
| `-d`, `--default=VALUE`   | Valor por defecto si el atributo no existe.                              | `crm_ticket -t ticketA -G granted -d false` |
| `-l`, `--info`            | Muestra el estado de los tickets.                                        | `crm_ticket -l`                      |
| `-C`, `--cleanup`         | Limpia el estado del ticket.                                            | `crm_ticket -t ticketA -C`           |
| `-f`, `--force`           | Fuerza la operación.                                                     | `crm_ticket -t ticketA -r -f`        |

# crm_standby
Atajo para poner/quitar un nodo en *standby* (no aloja recursos). Envoltorio de `crm_attribute`.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-N`, `--node=NAME`       | Nodo objetivo.                                                           | `crm_standby -N node1 -v on`         |
| `-v`, `--update=on\|off`  | Activa/desactiva el standby.                                             | `crm_standby -N node1 -v off`        |
| `-G`, `--query`           | Consulta el estado de standby.                                           | `crm_standby -N node1 -G`            |
| `-D`, `--delete`          | Elimina el atributo standby.                                            | `crm_standby -N node1 -D`            |
| `-l`, `--lifetime=LIFE`   | Duración: `reboot` (hasta reinicio) o `forever`.                         | `crm_standby -N node1 -v on -l reboot` |

# crm_failcount
Atajo para consultar/borrar contadores de fallo de un recurso en un nodo. Envoltorio de `crm_attribute`.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-r`, `--resource=ID`     | Recurso objetivo.                                                        | `crm_failcount -r web -G -N node1`   |
| `-N`, `--node=NAME`       | Nodo objetivo.                                                           | `crm_failcount -r web -N node1 -G`   |
| `-G`, `--query`           | Consulta el contador de fallos.                                          | `crm_failcount -r web -N node1 -G`   |
| `-D`, `--delete`          | Resetea el contador de fallos.                                          | `crm_failcount -r web -N node1 -D`   |
| `-n`, `--operation=OP`    | Limita a una operación concreta.                                         | `crm_failcount -r web -n monitor -G` |
| `-i`, `--interval=SPEC`   | Intervalo de la operación.                                               | `crm_failcount -r web -n monitor -i 10s -G` |

# crmadmin
Interacción con el controlador (controld): identifica el DC, consulta el estado de los nodos.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-D`, `--dc_lookup`       | Muestra qué nodo es el DC (Designated Coordinator).                      | `crmadmin -D`                        |
| `-N`, `--nodes`           | Lista los nodos del clúster.                                            | `crmadmin -N`                        |
| `-S`, `--status=NODE`     | Estado del controlador en un nodo.                                       | `crmadmin -S node1`                  |
| `-t`, `--timeout=MS`      | Timeout de la consulta en milisegundos.                                  | `crmadmin -D -t 60000`               |
| `-i`, `--debug-inc`       | Sube el nivel de debug del demonio.                                      | `crmadmin -i`                        |
| `-d`, `--debug-dec`       | Baja el nivel de debug del demonio.                                      | `crmadmin -d`                        |
| `-q`, `--quiet`           | Salida mínima (solo el dato).                                            | `crmadmin -D -q`                     |
| `-V`, `--verbose`         | Aumenta el detalle.                                                      | `crmadmin -S node1 -V`               |

# stonith_admin
Administración del subsistema de fencing/STONITH: dispositivos, niveles de topología y ejecución de acciones de aislamiento.

## Consultas
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-L`, `--list-registered` | Lista los dispositivos de fencing registrados.                           | `stonith_admin -L`                   |
| `-I`, `--list-installed`  | Lista los agentes STONITH instalados.                                    | `stonith_admin -I`                   |
| `-l`, `--list=NODE`       | Lista los dispositivos capaces de aislar a un nodo.                       | `stonith_admin -l node1`             |
| `-M`, `--metadata`        | Muestra los metadatos de un agente (`-a`).                              | `stonith_admin -M -a fence_ipmilan`  |
| `-H`, `--history=NODE`    | Muestra el histórico de fencing de un nodo (`*` = todos).                | `stonith_admin -H '*'`               |

## Registro y topología
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-R`, `--register=ID`     | Registra un dispositivo de fencing.                                      | `stonith_admin -R fence1 -a fence_ipmilan -o ...` |
| `-D`, `--deregister=ID`   | Elimina un dispositivo registrado.                                       | `stonith_admin -D fence1`            |
| `-r`, `--register-level=T`| Registra un nivel de topología de fencing para un target.                | `stonith_admin -r node1 -i 1 -v fence1` |
| `-d`, `--deregister-level=T`| Elimina un nivel de topología.                                         | `stonith_admin -d node1 -i 1`        |
| `-a`, `--agent=AGENT`     | Agente STONITH a usar.                                                   | `stonith_admin -M -a fence_virt`     |
| `-o`, `--option=K=V`      | Parámetro del dispositivo (repetible).                                   | `stonith_admin -R f1 -a fence_x -o ipaddr=10.0.0.1` |
| `-e`, `--env-option=K=V`  | Parámetro pasado como variable de entorno.                               | `stonith_admin -R f1 -a fence_x -e PASS=secret` |
| `-i`, `--index=N`         | Índice de nivel de topología (para `-r`/`-d`).                           | `stonith_admin -r node1 -i 2 -v f2`  |
| `-v`, `--victim/--devices`| Nodo o dispositivos asociados al nivel.                                  | `stonith_admin -r node1 -i 1 -v f1`  |

## Acciones de fencing
| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-F`, `--fence=NODE`      | Aísla (apaga) un nodo.                                                   | `stonith_admin -F node1`             |
| `-B`, `--reboot=NODE`     | Reinicia un nodo vía fencing.                                            | `stonith_admin -B node1`             |
| `-U`, `--unfence=NODE`    | Desaísla un nodo (fencing por recursos compartidos).                     | `stonith_admin -U node1`             |
| `-C`, `--confirm=NODE`    | Confirma manualmente que un nodo ha sido aislado.                        | `stonith_admin -C node1`             |
| `-c`, `--cleanup`         | Limpia el histórico de fencing.                                          | `stonith_admin -c`                   |
| `-t`, `--timeout=SEC`     | Timeout de la operación.                                                 | `stonith_admin -B node1 -t 120`      |
| `-T`, `--tag=TAG`         | Etiqueta de cliente para el registro.                                    | `stonith_admin -F node1 -T manual`   |
| `--tolerance=SEC`         | Reutiliza un fencing reciente dentro de la ventana indicada.             | `stonith_admin -F node1 --tolerance 60s` |
| `-V`, `--verbose`         | Aumenta el detalle.                                                      | `stonith_admin -L -V`                |
| `--output-as=FORMAT`      | Formato de salida (`text`, `xml`).                                       | `stonith_admin -H '*' --output-as=xml` |

# crm_error
Traduce códigos de retorno/salida de Pacemaker a su nombre y descripción simbólicos.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-l`, `--list`            | Lista todos los códigos conocidos.                                       | `crm_error -l`                       |
| `-n`, `--name`            | Muestra el nombre simbólico (además de la descripción).                  | `crm_error -n 201`                   |
| `-r`, `--rc`              | Interpreta los números como códigos de retorno (no de salida).           | `crm_error -r 1`                     |
| `CODE...`                 | Códigos a traducir (argumento posicional).                               | `crm_error 201`                      |

# crm_report
Recopila logs, configuración y estado de todos los nodos en un archivo comprimido para diagnóstico/soporte.

| **Flag**                  | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `-f`, `--from=TIME`       | Marca de tiempo de inicio del rango a recolectar.                        | `crm_report -f "2026-06-09 00:00" /tmp/rep` |
| `-t`, `--to=TIME`         | Marca de tiempo de fin del rango.                                        | `crm_report -f "..." -t "..." /tmp/rep` |
| `-n`, `--nodes=LIST`      | Lista de nodos a incluir.                                                | `crm_report -f "..." -n "node1 node2" /tmp/rep` |
| `-l`, `--logfile=FILE`    | Fichero de log adicional a incluir.                                      | `crm_report -f "..." -l /var/log/ha.log /tmp/rep` |
| `-S`, `--single-node`     | Recolecta solo del nodo local.                                           | `crm_report -f "..." -S /tmp/rep`    |
| `-d`, `--dest=DIR`        | Directorio/archivo de destino.                                          | `crm_report -f "..." -d /tmp/rep`    |
| `-v`, `--verbose`         | Aumenta el detalle.                                                      | `crm_report -f "..." -v /tmp/rep`    |
| `--features`              | Muestra las características soportadas y sale.                            | `crm_report --features`              |

# Propiedades del clúster de uso frecuente
Se fijan con `crm_attribute -t crm_config` (o vía [[pcs]] `property set`). No son flags de CLI sino claves de configuración esenciales.

| **Propiedad**             | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------- | ------------------------------------------------------------------------ | ------------------------------------ |
| `stonith-enabled`         | Habilita el fencing (debe estar `true` en producción).                   | `crm_attribute -n stonith-enabled -v true` |
| `no-quorum-policy`        | Acción sin quórum: `stop`, `ignore`, `freeze`, `suicide`, `demote`.       | `crm_attribute -n no-quorum-policy -v stop` |
| `maintenance-mode`        | Suspende la monitorización/gestión de todos los recursos.                | `crm_attribute -n maintenance-mode -v true` |
| `symmetric-cluster`       | Si los recursos pueden correr en cualquier nodo por defecto.             | `crm_attribute -n symmetric-cluster -v true` |
| `migration-limit`         | Nº máximo de migraciones en paralelo.                                    | `crm_attribute -n migration-limit -v 2` |
| `cluster-recheck-interval`| Frecuencia de reevaluación de reglas time-based.                         | `crm_attribute -n cluster-recheck-interval -v 5min` |

# Variables de entorno y rutas
| **Elemento**              | **Descripción**                                                          |
| ------------------------- | ------------------------------------------------------------------------ |
| `CIB_file`                | Hace que las herramientas operen sobre una CIB de fichero en lugar del clúster en vivo. |
| `CIB_shadow`              | Selecciona la shadow CIB activa (ver `crm_shadow`).                       |
| `CIB_user`                | Usuario efectivo para ACLs de la CIB.                                     |
| `PCMK_logfile`            | Fichero de log de Pacemaker (config en `/etc/sysconfig/pacemaker` o `/etc/default/pacemaker`). |
| `/var/lib/pacemaker/cib/` | Ubicación en disco de la CIB (`cib.xml`).                                 |

# Notas operativas
- En producción, `stonith-enabled=true` es obligatorio; sin fencing, Pacemaker puede negarse a recuperar recursos para evitar *split-brain*.
- Para cambios complejos, trabajar sobre una **shadow CIB** (`crm_shadow`) y validar con `crm_verify` + `crm_simulate` antes de `crm_shadow --commit`.
- `cibadmin --erase` y `crm_resource --force-*` son destructivas/fuera del control del clúster: usar solo en depuración.
- La administración cotidiana suele hacerse con [[pcs]] o `crmsh`, que generan internamente estas mismas llamadas.
