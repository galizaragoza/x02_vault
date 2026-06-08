snapper es una herramienta de gestión de instantáneas (snapshots) para sistemas de archivos Btrfs y volúmenes LVM (thin). Permite crear, comparar, revertir y limpiar automáticamente estados del sistema, lo que resulta vital para recuperarse de actualizaciones fallidas o errores de configuración. Opera sobre "configuraciones" (configs), cada una asociada a un subvolumen; la config `root` (`/`) es la habitual. Integraciones como `snap-pac` disparan snapshots pre/post automáticamente en cada transacción de `pacman`/`apt`/`zypper`.

```
snapper [opciones-globales] <subcomando> [opciones]
```

---

## Opciones globales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-c <config>` / `--config` | Configuración a usar (por defecto `root`). | `snapper -c home list` |
| `-r <ruta>` / `--root` | Opera sobre una raíz alternativa (sistema montado en otro punto). | `snapper -r /mnt list` |
| `--no-dbus` | No usa D-Bus; opera directamente (útil en entornos de rescate/chroot). | `snapper --no-dbus list` |
| `--iso` | Muestra fechas en formato ISO. | `snapper --iso list` |
| `--utc` | Muestra fechas en UTC. | `snapper --utc list` |
| `-t <estilo>` / `--table-style` | Estilo de la tabla de salida (0–6). | `snapper -t 2 list` |
| `--machine-readable <csv\|json>` / `--csvout` / `--jsonout` | Salida estructurada para scripts. | `snapper --jsonout list` |
| `-v` / `--verbose` | Salida detallada. | `snapper -v create` |
| `-q` / `--quiet` | Suprime salida. | `snapper -q delete 10` |
| `--version` | Muestra la versión. | `snapper --version` |
| `-h` / `--help` | Ayuda (global o de un subcomando). | `snapper create --help` |

---

## Gestión de configuraciones

| Subcomando | Descripción | Ejemplo |
|------------|-------------|---------|
| `create-config <subvol>` | Crea una configuración para un subvolumen. | `snapper -c datos create-config /mnt/datos` |
| `delete-config` | Elimina la configuración actual (`-c`). | `snapper -c datos delete-config` |
| `list-configs` | Lista todas las configuraciones definidas. | `snapper list-configs` |
| `get-config` | Muestra los parámetros de la configuración. | `snapper -c root get-config` |
| `set-config <clave=valor>` | Modifica parámetros de la configuración. | `snapper -c root set-config "TIMELINE_LIMIT_HOURLY=5"` |
| `setup-quota` | Configura las cuotas Btrfs para el seguimiento de espacio. | `snapper setup-quota` |

Parámetros frecuentes de configuración (fichero `/etc/snapper/configs/<nombre>`):

| Clave | Función |
|-------|---------|
| `TIMELINE_CREATE` | Activa snapshots periódicos (timeline). |
| `TIMELINE_CLEANUP` | Activa la limpieza automática de los timeline. |
| `TIMELINE_LIMIT_HOURLY/DAILY/WEEKLY/MONTHLY/YEARLY` | Cuántos snapshots conservar por periodo. |
| `NUMBER_CLEANUP` / `NUMBER_LIMIT` | Limpieza y límite de snapshots numerados (pre/post). |
| `ALLOW_USERS` / `ALLOW_GROUPS` | Usuarios/grupos con permiso sobre la config. |
| `BACKGROUND_COMPARISON` | Precalcula diffs en segundo plano. |

---

## Creación de instantáneas

| Opción de `create` | Descripción | Ejemplo |
|--------------------|-------------|---------|
| `--type <single\|pre\|post>` | Tipo: aislada (`single`), o par antes/después de una operación. | `snapper create --type single` |
| `-d <texto>` / `--description` | Descripción de la instantánea. | `snapper create -d "antes de actualizar"` |
| `-c <algoritmo>` / `--cleanup-algorithm` | Algoritmo de limpieza asociado: `number`, `timeline`, `empty-pre-post`. | `snapper create --cleanup-algorithm number` |
| `--pre-number <N>` | Asocia un snapshot `post` con su `pre`. | `snapper create --type post --pre-number 42` |
| `-u <k=v>` / `--userdata` | Metadatos arbitrarios clave=valor. | `snapper create -u "important=yes"` |
| `-k` / `--print-number` | Imprime el número del snapshot creado (para scripts). | `num=$(snapper create -p)` |
| `--command <cmd>` | Crea un `pre`, ejecuta el comando, y crea el `post` automáticamente. | `snapper create --command "pacman -Syu"` |

---

## Listado e inspección

| Subcomando / Opción | Descripción | Ejemplo |
|---------------------|-------------|---------|
| `list` | Lista las instantáneas de la config. | `snapper list` |
| `list -a` / `--all-configs` | Lista las de todas las configuraciones. | `snapper list -a` |
| `list -t <tipo>` / `--type` | Filtra por tipo (`all`, `single`, `pre-post`). | `snapper list -t pre-post` |
| `status <N1>..<N2>` | Lista los ficheros que cambiaron entre dos snapshots (sin contenido). | `snapper status 41..42` |
| `diff <N1>..<N2> [ficheros]` | Diferencias de contenido entre dos snapshots. | `snapper diff 1..2 /etc/fstab` |
| `xadiff <N1>..<N2>` | Diferencias de atributos extendidos y ACL. | `snapper xadiff 1..2` |

> Referencia de snapshot: un número (`5`), `0` = estado actual del sistema (live), rangos con `..` (`5..0` = del 5 al actual).

---

## Reversión de cambios

| Subcomando | Descripción | Ejemplo |
|------------|-------------|---------|
| `undochange <N1>..<N2> [ficheros]` | Revierte los cambios de ficheros del estado N1 al N2 (no toca el resto del sistema). | `snapper undochange 5..0 /etc` |
| `rollback [N]` | (Btrfs) Crea un snapshot del estado actual y establece N (o el último pre) como nuevo subvolumen raíz; requiere reinicio. | `snapper rollback 42` |
| `modify <N>` | Modifica metadatos de un snapshot existente (`-d`, `-c`, `-u`). | `snapper modify -d "revisado" 42` |

> **`undochange` vs `rollback`:** `undochange` deshace cambios a nivel de fichero dentro del sistema en ejecución; `rollback` sustituye el subvolumen raíz completo por un estado anterior (recuperación de sistema, exige reinicio).

---

## Borrado y limpieza

| Subcomando / Opción | Descripción | Ejemplo |
|---------------------|-------------|---------|
| `delete <N>` | Elimina una o varias instantáneas por ID. | `snapper delete 10 11 12` |
| `delete <N1>-<N2>` | Elimina un rango de instantáneas. | `snapper delete 10-20` |
| `delete --sync` | Borra y espera a que Btrfs libere el espacio. | `snapper delete 10 --sync` |
| `cleanup <algoritmo>` | Ejecuta manualmente un algoritmo de limpieza (`number`, `timeline`, `empty-pre-post`). | `snapper cleanup number` |

---

## Montaje de instantáneas

| Subcomando | Descripción | Ejemplo |
|------------|-------------|---------|
| `mount <N>` | Monta el snapshot N (acceso de solo lectura a su contenido). | `snapper mount 42` |
| `umount <N>` | Desmonta el snapshot. | `snapper umount 42` |

---

## Casos de uso comunes

```bash
# Snapshot manual antes de un cambio arriesgado
snapper -c root create -d "antes de tocar nginx"

# Par pre/post rodeando un comando
snapper create --command "pacman -Syu"

# Ver qué ficheros cambiaron entre dos estados
snapper status 41..42

# Diff de un fichero concreto
snapper diff 41..42 /etc/fstab

# Revertir solo /etc al estado del snapshot 41
snapper undochange 41..0 /etc

# Listar snapshots de todas las configs en formato JSON
snapper --jsonout list -a

# Borrar un rango y liberar espacio
snapper delete 10-30 --sync

# Recuperación completa del sistema (requiere reinicio)
snapper rollback 41
```
