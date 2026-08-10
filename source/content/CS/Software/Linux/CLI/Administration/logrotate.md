`logrotate` automatiza la rotación, compresión, eliminación y envío por correo de ficheros de log para evitar que crezcan sin límite. Se ejecuta normalmente de forma periódica (vía cron o el timer `logrotate.timer` de systemd) leyendo su configuración global (`/etc/logrotate.conf`) y los fragmentos por servicio en `/etc/logrotate.d/`. Mantiene un fichero de estado (`/var/lib/logrotate/status` o `/var/lib/logrotate.status`) para saber cuándo rotó cada log por última vez. Es declarativo: la lógica vive en directivas de configuración, no en flags.

```
logrotate [opciones] <fichero_config...>
```

---

## Opciones de línea de comandos

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-f`, `--force` | Fuerza la rotación aunque las condiciones no se cumplan. | `logrotate -f /etc/logrotate.conf` |
| `-d`, `--debug` | Modo depuración: implica `-v`, no modifica nada ni actualiza el estado. | `logrotate -d /etc/logrotate.d/nginx` |
| `-v`, `--verbose` | Salida detallada de cada acción. | `logrotate -v /etc/logrotate.conf` |
| `-s`, `--state <fichero>` | Usa un fichero de estado alternativo (útil sin privilegios). | `logrotate -s /tmp/state /etc/logrotate.conf` |
| `--skip-state-lock` | No bloquea el fichero de estado (evita fallo si el lock no es soportado). | `logrotate --skip-state-lock -f /etc/logrotate.conf` |
| `-m`, `--mail <comando>` | Comando usado para enviar logs por correo (por defecto `/bin/mail`). | `logrotate -m /usr/bin/mail /etc/logrotate.conf` |
| `-l`, `--log <fichero>` | Registra la salida de logrotate en el fichero indicado. | `logrotate -l /var/log/logrotate.log /etc/logrotate.conf` |
| `--version` | Muestra la versión. | `logrotate --version` |
| `-?`, `--help` | Muestra la ayuda. | `logrotate --help` |
| `--usage` | Muestra un resumen breve de uso. | `logrotate --usage` |

---

## Directivas de configuración

Las directivas se colocan en `/etc/logrotate.conf` (globales) o dentro de un bloque `ruta/al/log { ... }` (por log). Las locales tienen prioridad sobre las globales.

### Frecuencia y disparo

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `hourly` | Rota cada hora (requiere ejecución horaria). | `hourly` |
| `daily` | Rota una vez al día. | `daily` |
| `weekly [día]` | Rota semanalmente, opcionalmente en un día concreto (0=domingo). | `weekly 1` |
| `monthly` | Rota el primer día del mes. | `monthly` |
| `yearly` | Rota una vez al año. | `yearly` |
| `size <tam>` | Rota cuando el log supera el tamaño (`k`, `M`, `G`). Ignora la frecuencia. | `size 100M` |
| `minsize <tam>` | Rota según frecuencia, pero solo si se alcanza el tamaño mínimo. | `minsize 1M` |
| `maxsize <tam>` | Rota si se supera el tamaño aunque no toque por frecuencia. | `maxsize 50M` |

### Retención y conteo

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `rotate <n>` | Número de copias antiguas a conservar antes de borrar. | `rotate 7` |
| `maxage <días>` | Elimina copias rotadas con más de N días. | `maxage 30` |
| `start <n>` | Número inicial usado al numerar las copias rotadas. | `start 0` |

### Compresión

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `compress` | Comprime las copias rotadas (gzip por defecto). | `compress` |
| `nocompress` | No comprime. | `nocompress` |
| `delaycompress` | Retrasa la compresión a la siguiente rotación (la copia más reciente queda sin comprimir). | `delaycompress` |
| `nodelaycompress` | Desactiva `delaycompress`. | `nodelaycompress` |
| `compresscmd <cmd>` | Comando usado para comprimir. | `compresscmd /usr/bin/zstd` |
| `uncompresscmd <cmd>` | Comando usado para descomprimir. | `uncompresscmd /usr/bin/unzstd` |
| `compressext <ext>` | Extensión de los ficheros comprimidos. | `compressext .zst` |
| `compressoptions <opts>` | Opciones pasadas al comando de compresión. | `compressoptions -9` |

### Creación y manejo del fichero

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `create <modo> <user> <group>` | Crea un nuevo log vacío tras rotar, con permisos/dueño dados. | `create 0640 root adm` |
| `nocreate` | No crea un nuevo log tras rotar. | `nocreate` |
| `copytruncate` | Copia el log y luego lo trunca in situ (para procesos que no reabren el fichero). | `copytruncate` |
| `nocopytruncate` | Desactiva `copytruncate`. | `nocopytruncate` |
| `copy` | Copia el log sin tocar el original (no rota realmente). | `copy` |
| `nocopy` | Desactiva `copy`. | `nocopy` |
| `renamecopy` | Renombra a un temporal, copia al destino final y borra el temporal. | `renamecopy` |
| `shred` | Borra de forma segura (sobrescritura) en vez de `unlink`. | `shred` |
| `noshred` | Borra de forma normal. | `noshred` |
| `shredcycles <n>` | Número de pasadas de sobrescritura para `shred`. | `shredcycles 3` |

### Nombrado y destino

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `olddir <dir>` | Mueve las copias rotadas a otro directorio (mismo sistema de ficheros). | `olddir /var/log/old` |
| `noolddir` | Mantiene las copias en el directorio original. | `noolddir` |
| `createolddir <modo> <u> <g>` | Crea `olddir` si no existe, con esos permisos. | `createolddir 0750 root adm` |
| `nocreateolddir` | No crea `olddir` automáticamente. | `nocreateolddir` |
| `dateext` | Añade fecha (`-YYYYMMDD`) al nombre en vez de un número. | `dateext` |
| `nodateext` | Usa numeración en vez de fecha. | `nodateext` |
| `dateformat <fmt>` | Formato de la fecha (`%Y %m %d %H %s`). | `dateformat -%Y%m%d` |
| `dateyesterday` | Usa la fecha de ayer en `dateext`. | `dateyesterday` |
| `datehourago` | Usa la hora anterior en `dateext` (rotación horaria). | `datehourago` |
| `extension <ext>` | Conserva esta extension al final tras rotar (p. ej. `.log`). | `extension .log` |
| `addextension <ext>` | Añade esta extensión al nombre rotado. | `addextension .log` |

### Logs ausentes o vacíos

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `missingok` | No genera error si el log no existe. | `missingok` |
| `nomissingok` | Error si el log falta (por defecto). | `nomissingok` |
| `ifempty` | Rota aunque el log esté vacío (por defecto). | `ifempty` |
| `notifempty` | No rota si el log está vacío. | `notifempty` |

### Correo

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `mail <dirección>` | Envía por correo la copia que va a expirar. | `mail admin@dominio.com` |
| `nomail` | No envía nada por correo. | `nomail` |
| `mailfirst` | Envía la copia recién rotada en lugar de la que expira. | `mailfirst` |
| `maillast` | Envía la copia que está a punto de expirar (por defecto). | `maillast` |

### Scripts (pre/post)

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `prerotate` … `endscript` | Comandos ejecutados antes de rotar. | `prerotate`<br>`systemctl stop x`<br>`endscript` |
| `postrotate` … `endscript` | Comandos ejecutados después de rotar (típico: recargar el servicio). | `postrotate`<br>`systemctl reload nginx`<br>`endscript` |
| `firstaction` … `endscript` | Se ejecuta una vez antes de todas las rotaciones del bloque. | `firstaction … endscript` |
| `lastaction` … `endscript` | Se ejecuta una vez tras todas las rotaciones del bloque. | `lastaction … endscript` |
| `preremove` … `endscript` | Se ejecuta justo antes de borrar una copia antigua. | `preremove … endscript` |
| `sharedscripts` | Ejecuta los scripts una sola vez para todos los logs del patrón (no por cada fichero). | `sharedscripts` |
| `nosharedscripts` | Ejecuta los scripts por cada log (por defecto). | `nosharedscripts` |

### Globales y misceláneas

| **Directiva** | **Descripción** | **Ejemplo** |
|---------------|-----------------|-------------|
| `include <fichero/dir>` | Incluye configuración de otro fichero o directorio. | `include /etc/logrotate.d` |
| `su <user> <group>` | Rota usando ese usuario/grupo (necesario si `olddir` tiene otro dueño). | `su root adm` |
| `tabooext [+] <lista>` | Extensiones ignoradas al leer un directorio de configuración. | `tabooext + .bak` |
| `taboopat [+] <patrón>` | Patrones de fichero ignorados en directorios de config. | `taboopat + *.dpkg-*` |

---

## Ejemplo de bloque de configuración

```
/var/log/nginx/*.log {
    daily
    rotate 14
    missingok
    notifempty
    compress
    delaycompress
    create 0640 www-data adm
    sharedscripts
    postrotate
        systemctl reload nginx > /dev/null 2>&1 || true
    endscript
}
```

## Relacionados

- [[cronjobs]] / [[systemctl]] — disparo periódico (`logrotate.timer`).
- [[journalctl]] — retención de logs en systemd (alternativa al binario para journald).
