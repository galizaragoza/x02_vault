**cron** es el planificador de tareas periódicas de Unix. Un demonio (`crond`, provisto por implementaciones como *cronie*, *Vixie cron* o *dcron*) se despierta cada minuto, lee las tablas de tareas (*crontabs*), compara la hora actual con la expresión temporal de cada entrada y ejecuta las que coinciden. Cada implementación varía en detalles menores; los ejemplos aquí siguen **cronie/Vixie**, las más extendidas (Arch, Fedora, RHEL, Debian).

A diferencia de `[[at]]` (ejecución única en un instante futuro) o los *systemd timers* (ver `[[systemctl]]`), cron está pensado para tareas **recurrentes**. Para máquinas que no están siempre encendidas existe `anacron` (ver sección final).

---

## Sintaxis del campo temporal

Cada entrada de un crontab de usuario tiene la forma:

```sh
┌───────── minuto        (0-59)
│ ┌─────── hora          (0-23)
│ │ ┌───── día del mes   (1-31)
│ │ │ ┌─── mes           (1-12)  o  jan-dec
│ │ │ │ ┌─ día de semana (0-7)   0 y 7 = domingo, o sun-sat
│ │ │ │ │
* * * * *  comando a ejecutar
```

Ejemplos originales:

```sh
15 09 * * * /home/juser/bin/spmake   ## todos los días a las 09:15
15 09 5,14 * * /home/juser/bin/spmake ## los días 5 y 14 de cada mes a las 09:15
```

| **Unidad**       | **Rango**              | **Nombres admitidos** |
| ---------------- | ---------------------- | --------------------- |
| Minuto           | 0-59                   | —                     |
| Hora             | 0-23                   | —                     |
| Día del mes      | 1-31                   | —                     |
| Mes              | 1-12                   | `jan`..`dec`          |
| Día de la semana | 0-7 (0 y 7 = domingo)  | `sun`..`sat`          |

![[cron.png]]

### Operadores dentro de un campo

| **Operador** | **Significado**                        | **Ejemplo**   | **Se ejecuta**                          |
| ------------ | -------------------------------------- | ------------- | --------------------------------------- |
| `*`          | Todos los valores del rango            | `* * * * *`   | Cada minuto                             |
| `,`          | Lista de valores discretos             | `0 8,12,18 * * *` | A las 08:00, 12:00 y 18:00          |
| `-`          | Rango contiguo                         | `0 9-17 * * *`| Cada hora en punto de 09 a 17           |
| `/`          | Paso / incremento                      | `*/15 * * * *`| Cada 15 min (0,15,30,45)                |
| `-` + `/`    | Paso dentro de un rango                | `0-30/10 * * *`| Minutos 0,10,20,30                     |

> [!warning] Lógica OR entre día-del-mes y día-de-semana
> Si **ambos** campos (día del mes *y* día de la semana) están restringidos (ninguno es `*`), cron ejecuta la tarea cuando coincide **cualquiera** de los dos, no cuando coinciden ambos. `0 0 13 * 5` ejecuta el día 13 **y además** todos los viernes. Para condiciones "día 13 que además sea viernes" hay que comprobarlo con `test` dentro del propio comando.

---

## Shortcuts especiales

Sustituyen a los cinco campos temporales:

| **Shortcut**         | **Equivalente** | **Descripción**                                     |
| -------------------- | --------------- | --------------------------------------------------- |
| `@reboot`            | —               | Una vez, al arrancar el demonio cron                |
| `@yearly` / `@annually` | `0 0 1 1 *`  | 1 de enero a medianoche                             |
| `@monthly`           | `0 0 1 * *`     | Primer día del mes a medianoche                     |
| `@weekly`            | `0 0 * * 0`     | Domingo a medianoche                                |
| `@daily` / `@midnight` | `0 0 * * *`   | Cada día a medianoche                               |
| `@hourly`            | `0 * * * *`     | Al minuto 0 de cada hora                            |

> `@reboot` es útil para lanzar procesos de usuario al inicio sin escribir una unidad systemd, pero se ejecuta cuando arranca `crond`, no cuando el sistema termina de bootear.

---

## El comando `crontab`

Gestiona el crontab **por usuario**. Sin argumentos de fichero opera sobre la tabla del usuario invocante.

| **Parámetro** | **Función**                                                            | **Ejemplo**                 |
| ------------- | ---------------------------------------------------------------------- | --------------------------- |
| `-e`          | Edita el crontab actual (usa `$VISUAL` o `$EDITOR`).                    | `crontab -e`                |
| `-l`          | Lista el contenido del crontab actual.                                 | `crontab -l`                |
| `-r`          | Elimina el crontab actual.                                             | `crontab -r`                |
| `-u user`     | Actúa sobre el crontab de otro usuario (requiere privilegios).         | `crontab -u usuario -l`     |
| `-i`          | Pide confirmación antes de eliminar (con `-r`).                        | `crontab -r -i`             |
| `file`        | Instala un crontab desde fichero (reemplaza el existente).            | `crontab nuevo_crontab.txt` |
| `-V`          | Muestra la versión y sale. *(no en todas las implementaciones)*        | `crontab -V`                |
| `-h`          | Muestra la ayuda y sale.                                               | `crontab -h`                |

> [!tip] Copia de seguridad antes de editar
> `crontab -l > ~/crontab.bak`. La tecla peligrosa es `r` junto a `e` en el teclado: `crontab -r` borra la tabla sin preguntar. Usa siempre `crontab -e` para editar y `-i` si combinas con `-r`.

---

## Tipos de crontab y ubicación de ficheros

Existen dos familias de crontabs con **sintaxis distinta**.

### 1. Crontab de usuario

- Se editan con `crontab -e`, nunca a mano.
- Almacenados en el *spool*: `/var/spool/cron/<usuario>` (RHEL/Arch/cronie) o `/var/spool/cron/crontabs/<usuario>` (Debian/Ubuntu).
- **5 campos temporales + comando.** El comando corre como el usuario propietario.

### 2. Crontab del sistema

- Ficheros de texto plano editables directamente: `/etc/crontab` y fragmentos en `/etc/cron.d/`.
- Llevan un **6.º campo adicional: el usuario** con el que se ejecuta la tarea.

```sh
# /etc/crontab  —  min hora dom mes dow  USUARIO  comando
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
0  3    * * *   backup  /usr/local/bin/backup.sh
```

### 3. Directorios `run-parts`

Muchas distribuciones traen directorios donde basta con **dejar un script ejecutable** sin escribir expresión cron; `/etc/crontab` los recorre con `run-parts`:

| **Directorio**      | **Frecuencia típica** |
| ------------------- | --------------------- |
| `/etc/cron.hourly/` | Cada hora             |
| `/etc/cron.daily/`  | Cada día              |
| `/etc/cron.weekly/` | Cada semana           |
| `/etc/cron.monthly/`| Cada mes              |

> [!warning] `run-parts` ignora ciertos nombres de fichero
> Por defecto `run-parts` **solo ejecuta ficheros cuyo nombre contiene únicamente** letras `[a-zA-Z]`, dígitos `[0-9]`, guiones bajos `_` y guiones `-`. Un script llamado `backup.sh` (con punto) **no se ejecuta**: renómbralo a `backup`. Verifica qué correría con `run-parts --test /etc/cron.daily`.

---

## Entorno de ejecución

Es la causa nº 1 de "el script funciona en mi terminal pero no en cron". El entorno de cron es **mínimo** y no es un login shell:

- `SHELL=/bin/sh`, `PATH` reducido (normalmente `/usr/bin:/bin`), sin tu `~/.bashrc`, `~/.profile` ni variables de tu sesión.
- `HOME` y `LOGNAME` provienen de `/etc/passwd`.
- El *working directory* es `HOME`.

### Variables reconocidas en el crontab

Se declaran como líneas `VAR=valor` antes de las entradas:

| **Variable**   | **Efecto**                                                                 |
| -------------- | -------------------------------------------------------------------------- |
| `SHELL`        | Shell con el que se interpreta el comando.                                 |
| `PATH`         | Rutas de búsqueda de binarios. Amplíalo o usa **rutas absolutas** siempre. |
| `MAILTO`       | Destinatario del correo con la salida. `MAILTO=""` desactiva el correo.    |
| `MAILFROM`     | Remitente del correo *(cronie)*.                                           |
| `CRON_TZ`      | Zona horaria para las entradas siguientes *(cronie/Vixie)*.                |
| `RANDOM_DELAY` | Retardo aleatorio máximo en minutos antes de ejecutar *(cronie)*.          |

```sh
PATH=/usr/local/bin:/usr/bin:/bin
MAILTO=admin@example.com
CRON_TZ=UTC

0 2 * * * /usr/local/bin/backup.sh
```

> [!tip] Regla de oro
> Usa **rutas absolutas** para binarios y ficheros, y no asumas ninguna variable de entorno. Si el script las necesita, cárgalas explícitamente: `0 2 * * * . $HOME/.profile; /ruta/script.sh`.

---

## Salida, correo y logging

Si una tarea produce salida por *stdout*/*stderr* y no se redirige, cron intenta **enviarla por correo** al usuario (vía `MAILTO`/MTA local). Sin MTA, esa salida se pierde y solo queda rastro en el log del sistema.

| **Patrón**                     | **Efecto**                                             |
| ------------------------------ | ------------------------------------------------------ |
| `>> /var/log/job.log 2>&1`     | Añade stdout **y** stderr al fichero de log.           |
| `> /var/log/job.log 2>&1`      | Sobrescribe el log en cada ejecución.                  |
| `> /dev/null 2>&1`             | Descarta toda la salida (silencia el correo).          |
| `2>&1 \| logger -t mi_tarea`   | Envía la salida al syslog con una etiqueta.            |
| `MAILTO="" `                   | Desactiva el correo globalmente para ese crontab.      |

```sh
# Registrar todo en un log propio con marca de tarea
30 3 * * * /usr/local/bin/sync.sh >> /var/log/sync.log 2>&1
```

> [!warning] El signo `%` es especial
> En el campo comando, un `%` sin escapar se convierte en **salto de línea**, y todo lo que sigue al primer `%` se pasa como **stdin** al comando. Escápalos con `\%`. Afecta sobre todo a `date`: usa `date +\%Y-\%m-\%d` dentro de un crontab.

---

## Control de acceso

Quién puede usar `crontab` se decide con dos ficheros (rutas habituales: `/etc/cron.allow`, `/etc/cron.deny`):

- Si existe `cron.allow`: **solo** los usuarios listados pueden usar cron; `cron.deny` se ignora.
- Si no existe `cron.allow` pero sí `cron.deny`: todos **excepto** los listados.
- Si no existe ninguno: según distribución, solo root o todos los usuarios.

`root` siempre puede usar cron independientemente de estos ficheros.

---

## Depuración y buenas prácticas

Checklist cuando "el cron no se ejecuta":

1. **¿Corre el demonio?** `systemctl status cronie` (o `cron`, `crond` según distro). Ver `[[systemctl]]`.
2. **¿Se registró la ejecución?** cron loguea cada disparo aunque el comando falle:
   - systemd: `journalctl -u cronie` / `journalctl -u cron` / `journalctl -u crond`, o por etiqueta `journalctl -t CRON`.
   - syslog clásico: `grep CRON /var/log/syslog` (Debian) o `/var/log/cron` (RHEL). Ver `[[journalctl]]`, `[[syslog-ng]]`.
3. **¿Rutas absolutas?** Sustituye `python` por `/usr/bin/python3`, etc.
4. **¿Entorno?** Redirige la salida a un fichero (`>> /tmp/debug.log 2>&1`) y examina el error real.
5. **¿Salto de línea final?** Un crontab de usuario debe terminar con newline; algunas implementaciones ignoran la última línea sin él.
6. **¿Permisos de ejecución?** El script debe ser ejecutable (`chmod +x`) o invocarse con su intérprete (`/bin/sh script.sh`).

### Evitar solapamientos con `flock`

Si una ejecución puede durar más que el intervalo, dos instancias pueden pisarse. `flock` toma un lock exclusivo y omite la ejecución si ya hay otra en curso:

```sh
# -n: no bloqueante; si el lock está tomado, sale sin ejecutar
*/5 * * * * /usr/bin/flock -n /tmp/sync.lock /usr/local/bin/sync.sh
```

### Simular el entorno de cron para probar

```sh
# Ejecuta el comando con un entorno reducido, similar al de cron
env -i /bin/sh -c '/usr/local/bin/sync.sh'
```

---

## Ejemplos prácticos

```sh
# Cada 5 minutos
*/5 * * * * /usr/local/bin/check.sh

# De lunes a viernes, cada hora entre las 9 y las 18
0 9-18 * * 1-5 /usr/local/bin/report.sh

# El primer lunes de cada mes (día 1-7 + comprobar que es lunes)
0 6 1-7 * 1 [ "$(date +\%u)" = "1" ] && /usr/local/bin/monthly.sh

# Backup diario a las 02:30, con log y sin solapamiento
30 2 * * * /usr/bin/flock -n /tmp/bk.lock /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1

# Al arranque, arrancar un servicio de usuario
@reboot /home/user/bin/start-tunnel.sh
```

---

## Alternativas y relacionados

| **Herramienta**   | **Uso**                                                                             |
| ----------------- | ----------------------------------------------------------------------------------- |
| `[[at]]`          | Ejecución **única** en un instante futuro.                                          |
| `[[systemctl]]` (timers) | Planificación con dependencias, logging integrado en journald, `OnCalendar=`, persistencia y retardos aleatorios. Sustituto moderno de cron en sistemas systemd. |
| `anacron`         | Para máquinas que **no están siempre encendidas** (portátiles). Ver abajo.          |

### anacron

cron asume que la máquina está encendida a la hora prevista; si estaba apagada, **la ejecución se pierde**. `anacron` trabaja con **granularidad de días** y garantiza que las tareas se ejecuten aunque el equipo estuviera apagado, recuperándolas al encender. Se configura en `/etc/anacrontab`:

```sh
# periodo(días)  retardo(min)  identificador     comando
1               5             cron.daily        run-parts /etc/cron.daily
7               10            cron.weekly       run-parts /etc/cron.weekly
@monthly        15            cron.monthly      run-parts /etc/cron.monthly
```

En muchas distros de escritorio, `/etc/cron.{daily,weekly,monthly}` los dispara **anacron**, no cron, precisamente para no perder ejecuciones en equipos que se apagan.

---

## Relevancia en seguridad

cron es un vector clásico de **persistencia** y **escalada de privilegios** en Linux:

- Un crontab de root que ejecuta un script con permisos de escritura para un usuario sin privilegios permite ejecutar código como root.
- Directorios `/etc/cron.d/`, `/etc/cron.*/` o el spool con permisos laxos permiten inyectar tareas.
- Como persistencia, un atacante añade una entrada (a menudo `@reboot` o un intervalo corto) que reestablece una conexión.

En auditoría, revisar: `crontab -l` de cada usuario, `/etc/crontab`, `/etc/cron.d/`, `/etc/cron.*/`, el spool `/var/spool/cron/`, y los permisos de todo script referenciado.
