`syslog-ng` es un demonio de recolección y enrutado de logs, alternativa avanzada a `rsyslog`/`syslogd`. Recibe mensajes de múltiples fuentes (sockets, ficheros, red, journald), los procesa (filtrado, parseo, reescritura, plantillas) y los envía a destinos diversos (ficheros, red, SQL, Elasticsearch, programas). Su configuración (`/etc/syslog-ng/syslog-ng.conf`) es declarativa y se basa en un *pipeline*: `source → filter/parser/rewrite → destination`, unidos por sentencias `log`. Esta es una cheatsheet de su CLI, su utilidad de control y los bloques de configuración.

```
syslog-ng [opciones]
```

---

## CLI del demonio `syslog-ng`

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-f`, `--cfgfile <fichero>` | Usa un fichero de configuración alternativo. | `syslog-ng -f /etc/syslog-ng/test.conf` |
| `-s`, `--syntax-only` | Verifica la sintaxis de la config y sale. | `syslog-ng -s` |
| `--preprocess-into <fichero>` | Vuelca la config preprocesada (macros incluidas) a un fichero. | `syslog-ng --preprocess-into=/tmp/out.conf` |
| `-F`, `--foreground` | No se desacopla; corre en primer plano. | `syslog-ng -F` |
| `-d`, `--debug` | Mensajes de depuración. | `syslog-ng -d` |
| `-v`, `--verbose` | Mensajes verbosos. | `syslog-ng -v` |
| `-t`, `--trace` | Traza el procesamiento de cada mensaje. | `syslog-ng -t` |
| `-e`, `--stderr` | Envía los mensajes internos a stderr. | `syslog-ng -Fe` |
| `-p`, `--pidfile <fichero>` | Fichero de PID a usar. | `syslog-ng -p /run/syslog-ng.pid` |
| `--control <fichero>` | Socket de control para `syslog-ng-ctl`. | `syslog-ng --control=/run/syslog-ng.ctl` |
| `--persist-file <fichero>` | Fichero de estado persistente (posiciones, etc.). | `syslog-ng --persist-file=/var/lib/syslog-ng/syslog-ng.persist` |
| `-C`, `--chroot <dir>` | Ejecuta dentro de un chroot. | `syslog-ng -C /var/chroot` |
| `-u`, `--user <usuario>` | Cambia al usuario indicado tras arrancar. | `syslog-ng -u syslog` |
| `-g`, `--group <grupo>` | Cambia al grupo indicado. | `syslog-ng -g adm` |
| `--no-caps` | No usa capabilities de Linux (corre con permisos completos). | `syslog-ng --no-caps` |
| `--enable-core` | Permite generar core dumps ante fallo. | `syslog-ng --enable-core` |
| `--fd-limit <n>` | Límite de descriptores de fichero. | `syslog-ng --fd-limit=8192` |
| `--worker-threads <n>` | Número máximo de hilos de trabajo. | `syslog-ng --worker-threads=4` |
| `-V`, `--version` | Versión y módulos compilados. | `syslog-ng -V` |
| `--help` | Ayuda. | `syslog-ng --help` |

## Control en caliente: `syslog-ng-ctl`

| **Subcomando** | **Descripción** | **Ejemplo** |
|----------------|-----------------|-------------|
| `stats` | Estadísticas de mensajes procesados/descartados por fuente y destino. | `syslog-ng-ctl stats` |
| `stats --reset` | Reinicia los contadores de estadísticas. | `syslog-ng-ctl stats --reset` |
| `query <patrón>` | Consulta contadores concretos del subsistema de stats. | `syslog-ng-ctl query "*.dropped"` |
| `reload` | Recarga la configuración sin perder mensajes en buffer. | `syslog-ng-ctl reload` |
| `reopen` | Reabre los ficheros de salida (tras una rotación de logs). | `syslog-ng-ctl reopen` |
| `stop` | Detiene el demonio. | `syslog-ng-ctl stop` |
| `verbose --set=on\|off` | Activa/desactiva mensajes verbosos en caliente. | `syslog-ng-ctl verbose --set=on` |
| `debug --set=on\|off` | Activa/desactiva depuración en caliente. | `syslog-ng-ctl debug --set=on` |
| `trace --set=on\|off` | Activa/desactiva traza en caliente. | `syslog-ng-ctl trace --set=on` |
| `config` | Vuelca la configuración en ejecución. | `syslog-ng-ctl config` |
| `healthcheck` | Comprueba el estado de salud del demonio. | `syslog-ng-ctl healthcheck` |
| `license-check` | Información de licencia (edición PE). | `syslog-ng-ctl license-check` |

---

## Bloques de configuración

La configuración define objetos (`source`, `destination`, `filter`, `parser`, `rewrite`, `template`) y los conecta con sentencias `log { ... }`.

| **Bloque** | **Descripción** | **Ejemplo** |
|------------|-----------------|-------------|
| `@version: X.Y` | Declara la versión de sintaxis (obligatorio al inicio). | `@version: 4.0` |
| `source s_id { ... }` | Define de dónde llegan los mensajes. | `source s_local { system(); internal(); };` |
| `destination d_id { ... }` | Define a dónde se envían. | `destination d_file { file("/var/log/all.log"); };` |
| `filter f_id { ... }` | Condición booleana para seleccionar mensajes. | `filter f_err { level(err..emerg); };` |
| `parser p_id { ... }` | Extrae campos estructurados (JSON, CSV, key-value, patterndb). | `parser p_json { json-parser(prefix(".json.")); };` |
| `rewrite r_id { ... }` | Modifica el contenido o las macros del mensaje. | `rewrite r_set { set("masked", value("password")); };` |
| `template t_id { ... }` | Formato de salida usando macros. | `template t_iso { template("$ISODATE $HOST $MSG\n"); };` |
| `log { ... }` | Conecta source/filter/parser/destination en un pipeline. | ver abajo |
| `options { ... }` | Opciones globales del demonio. | `options { keep-hostname(yes); chain-hostnames(no); };` |

### Drivers de `source` habituales

| **Driver** | **Descripción** | **Ejemplo** |
|------------|-----------------|-------------|
| `system()` | Detecta automáticamente la fuente local de logs del SO. | `system();` |
| `internal()` | Mensajes propios de syslog-ng. | `internal();` |
| `file(...)` | Lee de un fichero. | `file("/var/log/app.log");` |
| `unix-stream(...)` / `unix-dgram(...)` | Socket Unix. | `unix-stream("/dev/log");` |
| `network(...)` | Recepción por red (TCP/UDP, RFC3164/5424, TLS). | `network(transport("tls") port(6514));` |
| `syslog(...)` | Recepción protocolo syslog estándar. | `syslog(transport("udp") port(514));` |
| `systemd-journal()` | Lee del journal de systemd. | `systemd-journal();` |

### Drivers de `destination` habituales

| **Driver** | **Descripción** | **Ejemplo** |
|------------|-----------------|-------------|
| `file(...)` | Escribe en fichero (admite macros en la ruta). | `file("/var/log/$HOST/$PROGRAM.log");` |
| `network(...)` | Reenvío por red a otro host/colector. | `network("10.0.0.5" transport("tcp") port(514));` |
| `syslog(...)` | Envío con protocolo syslog. | `syslog("siem.local" port(601));` |
| `program(...)` | Envía a la entrada estándar de un programa. | `program("/usr/bin/myproc");` |
| `elasticsearch-http(...)` | Indexa en Elasticsearch. | `elasticsearch-http(url("http://es:9200/_bulk"));` |
| `usertty(...)` | Envía a la terminal de un usuario. | `usertty("root");` |

### Filtros habituales

| **Función** | **Descripción** | **Ejemplo** |
|-------------|-----------------|-------------|
| `level(...)` | Filtra por prioridad. | `level(warning..emerg)` |
| `facility(...)` | Filtra por facility. | `facility(auth, authpriv)` |
| `program(...)` | Filtra por nombre de programa. | `program("sshd")` |
| `host(...)` | Filtra por host de origen (regex). | `host("web0[1-9]")` |
| `match(...)` | Coincidencia regex contra un campo. | `match("failed" value("MESSAGE"))` |

### Pipeline `log`

```
@version: 4.0
@include "scl.conf"

source s_net { syslog(transport("tcp") port(514)); };
filter  f_err { level(err..emerg); };
destination d_err { file("/var/log/errors.log"); };

log {
    source(s_net);
    filter(f_err);
    destination(d_err);
    flags(final);
};
```

> `flags(final)` detiene el procesamiento del mensaje en este `log` si coincide; `flags(flow-control)` aplica contrapresión para no perder mensajes.

## Relacionados

- [[journalctl]] — fuente vía `systemd-journal()`; syslog-ng puede sustituir o complementar a journald.
- [[logrotate]] — rotar las salidas `file()`; tras rotar, ejecutar `syslog-ng-ctl reopen`.
