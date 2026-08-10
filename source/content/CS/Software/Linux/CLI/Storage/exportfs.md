`exportfs` mantiene la tabla de sistemas de archivos locales exportados por un servidor NFS hacia sus clientes. Permite añadir, eliminar y refrescar exports sin tener que editar `/etc/exports` ni recargar el servicio completo, escribiendo la tabla maestra en `/var/lib/nfs/etab`. No habla con el kernel directamente: delega en sus programas asociados `rpc.mountd` y `nfsv4.exportd`, que operan en modo netlink (kernels recientes) o `/proc`. Es la herramienta central de administración de exports NFS en el lado servidor.

```
/usr/sbin/exportfs [-avi] [-o opciones,..] [cliente:/ruta ..]   # exportar
/usr/sbin/exportfs -r [-v]                                       # reexportar (sincronizar)
/usr/sbin/exportfs [-av] -u [cliente:/ruta ..]                  # desexportar
/usr/sbin/exportfs [-v]                                          # listar exports actuales
/usr/sbin/exportfs -f                                            # flush de la tabla del kernel
/usr/sbin/exportfs -s                                            # volcar en formato /etc/exports
```

> Opciones por defecto de cada export: `sync,ro,root_squash,wdelay`. Se sobrescriben con entradas en `/etc/exports`/`/etc/exports.d` o con `-o` en la línea de comandos.

---

## Exportar y desexportar directorios

Gestionan el alta y baja de entradas en `/var/lib/nfs/etab` y en la tabla del kernel.

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-a` | Exporta (o desexporta, con `-u`) **todos** los directorios listados en `/etc/exports` y `/etc/exports.d`. | `exportfs -a` |
| `-o opciones,...` | Especifica opciones de export en la misma sintaxis que `exports(5)`. Sobrescribe las de otras fuentes; permite modificar un export ya existente. | `exportfs -o insecure_locks django:/usr/tmp` |
| `-i` | Ignora `/etc/exports` y `/etc/exports.d`. Solo se usan las opciones por defecto y las dadas en la línea de comandos. | `exportfs -i -o rw,no_root_squash 10.0.0.5:/srv` |
| `-u` | Desexporta uno o más directorios (elimina su entrada de `etab` y la del kernel). | `exportfs -u django:/usr/tmp` |
| `cliente:/ruta` | Argumento posicional: directorio local a exportar y el/los cliente(s) autorizados. Ver tabla de sintaxis de cliente más abajo. | `exportfs 192.168.1.0/24:/srv/nfs` |

---

## Sincronización y caché del kernel

Reconcilian la tabla en disco con `/etc/exports` y con la tabla del kernel.

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-r` | Reexporta todo, sincronizando `/var/lib/nfs/etab` con `/etc/exports` y `/etc/exports.d`. Elimina entradas borradas de la config y entradas del kernel ya no válidas. | `exportfs -r` |
| `-f` | Si `/proc/fs/nfsd` o `/proc/fs/nfs` está montado, vacía (flush) toda la tabla de exports del kernel. `rpc.mountd` repuebla entradas en la siguiente petición de montaje. | `exportfs -f` |
| `-L` | Deshabilita netlink y fuerza el uso de las interfaces `/proc` legadas para el flush de caché y la validación de exports. | `exportfs -L -f` |

---

## Visualización de la tabla de exports

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| *(sin opciones)* | Muestra la lista actual de FS exportados. | `exportfs` |
| `-v` | Modo verbose. Al exportar/desexportar muestra qué ocurre; al listar, añade las opciones de cada export. | `exportfs -v` |
| `-s` | Vuelca la lista actual de exports en formato apto para `/etc/exports`. | `exportfs -s` |

---

## Depuración

| **Parámetro** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `-d tipo`, `--debug tipo` | Activa depuración. Tipos válidos: `all`, `auth`, `call`, `general`, `parse`. También configurable vía `debug=` en la sección `[exportfs]` de `/etc/nfs.conf`. | `exportfs -d all -a` |

---

## Sintaxis del especificador de cliente

El argumento `cliente:/ruta` admite varias formas para definir quién accede al export.

| **Forma** | **Función** | **Ejemplo de Sintaxis** |
| --- | --- | --- |
| `host:/ruta` | Exporta a un host concreto (por nombre o IP). | `exportfs cliente1:/srv/data` |
| `:/ruta` | Exporta al mundo (sin restricción de host). | `exportfs :/srv/public` |
| `red/máscara:/ruta` | Exporta a una red IPv4 (CIDR). | `exportfs 192.168.0.0/24:/srv/data` |
| `[IPv6]/prefijo:/ruta` | Cliente IPv6: la dirección va entre corchetes (los `:` ya separan host y ruta); el prefijo de red va tras el corchete de cierre. | `exportfs [fe80::]/64:/usr/tmp` |
| `dominio NIS/DNS` | Exporta a un grupo de dominio (hint para `rpc.mountd`, no concede acceso inmediato). | `exportfs '*.ejemplo.com:/srv/data'` |

---

## Ficheros relevantes

| **Ruta** | **Función** |
| --- | --- |
| `/etc/exports` | Fichero de entrada: lista de exports, opciones y ACLs de acceso. |
| `/etc/exports.d` | Directorio con ficheros de entrada adicionales (solo se usan los que terminan en `.exports`). |
| `/var/lib/nfs/etab` | Tabla maestra de exports (leída por `rpc.mountd` ante un MOUNT). |
| `/var/lib/nfs/rmtab` | Tabla de clientes que acceden a los exports del servidor. |
| `/etc/nfs.conf` | Config; la sección `[exportfs]` admite `debug=`. Se reconocen `state-directory-path` y `no-netlink` de `[mountd]`/`[exportd]`. |

---

## Modos de comunicación con el kernel

`exportfs` no escribe directamente en el kernel; sus daemons asociados operan en uno de dos modos:

- **Netlink** (kernels recientes): `rpc.mountd`/`nfsv4.exportd` hablan con el kernel vía generic netlink. El kernel emite notificaciones multicast cuando hay entradas de caché que resolver y el daemon responde con la info del export. El flush (`-f`) también va por netlink. Se desactiva con `-L` o con `no-netlink` en `nfs.conf`.
- **`/proc`** (cuando netlink no está disponible): `rpc.mountd` gestiona las peticiones del kernel a través de los ficheros de canal en `/proc/net/rpc`.

---

## Ejemplos prácticos

```sh
exportfs -a                                 # exporta todo lo definido en /etc/exports(.d) y lo empuja al kernel
exportfs -o insecure_locks django:/usr/tmp  # export puntual permitiendo file locking inseguro
exportfs -u django:/usr/tmp                 # desexporta /usr/tmp para django
exportfs -au                                # desexporta todo (corta efectivamente la actividad NFS)
exportfs [fe80::]/64:/usr/tmp               # exporta a clientes IPv6 link-local
exportfs -rv                                # reexporta sincronizando, verbose
exportfs -v                                 # lista exports con sus opciones
```

---

Relacionado: [[NFS]] · [[(u)mount]] · [[findmnt]] · [[SSHFS]]
