`ntpq` (NTP query) es el cliente estándar para interrogar al demonio `ntpd` mediante el protocolo de control NTP (modo 6, sobre UDP/123). Permite consultar el estado de la sincronización, la lista de *peers*, las variables internas del reloj y estadísticas operativas, así como —con autenticación— reconfigurar el demonio en caliente. Opera de dos formas: ejecutando comandos sueltos con `-c`/`-p` y saliendo, o entrando en una **sesión interactiva** con su propio intérprete. Es la herramienta de diagnóstico de referencia para auditar la salud de un servidor NTP. Véase también [[ntp]].

```
ntpq [-46dinpw] [-c comando]... [host...]
```

> Sin argumentos consulta `localhost`. Cada `host` posicional es un servidor `ntpd` a interrogar. El campo *tally* (primer carácter de cada línea de `peers`) y el *reach* (octal) son las dos lecturas clave para evaluar la sincronización; ver la tabla de códigos al final.

---

# Opciones de línea de comandos

| Opción | Función | Ejemplo |
|--------|---------|---------|
| `-4`, `--ipv4` | Fuerza la resolución de nombres a IPv4. | `ntpq -4 -p pool.ntp.org` |
| `-6`, `--ipv6` | Fuerza la resolución de nombres a IPv6. | `ntpq -6 -p ntp.example.net` |
| `-c CMD`, `--command=CMD` | Ejecuta `CMD` como comando interactivo y sale; puede repetirse para encadenar comandos. | `ntpq -c rv -c peers` |
| `-d`, `--debug-level` | Incrementa el nivel de depuración; puede repetirse. | `ntpq -d -d -c peers` |
| `-D N`, `--set-debug-level=N` | Fija el nivel de depuración a `N`. | `ntpq -D 3 -c rv` |
| `-i`, `--interactive` | Fuerza el modo interactivo aunque se pasen comandos por línea. | `ntpq -i` |
| `-n`, `--numeric` | Muestra las direcciones de host en forma numérica (no resuelve DNS inverso). | `ntpq -n -p` |
| `-p`, `--peers` | Imprime la lista de *peers* y sale (equivale al comando `peers`). | `ntpq -p` |
| `-w`, `--wide` | Salida ancha: no trunca nombres/direcciones largas (útil con IPv6). | `ntpq -w -p` |
| `--help` | Muestra la ayuda de uso. | `ntpq --help` |
| `--version` | Muestra la versión. | `ntpq --version` |

---

# Control de sesión (modo interactivo)

Comandos que configuran el comportamiento del propio `ntpq`, no del demonio.

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `? [cmd]`, `help [cmd]` | Lista comandos o muestra la ayuda de uno. | `help readvar` |
| `host [nombre]` | Cambia el servidor `ntpd` que se está consultando. | `host ntp1.example.net` |
| `hostnames [yes\|no]` | Activa/desactiva la resolución de nombres en la salida. | `hostnames no` |
| `ntpversion [1-4]` | Fija la versión NTP de los paquetes de consulta. | `ntpversion 4` |
| `keyid KEYID` | Establece el keyid usado para autenticar comandos de control. | `keyid 1` |
| `keytype TIPO` | Algoritmo del MAC de autenticación (`md5`, `sha1`…). | `keytype sha1` |
| `passwd` | Pide (interactivamente) la clave asociada al `keyid`. | `passwd` |
| `delay MS` | Añade un retardo de procesado a los paquetes de control salientes. | `delay 0` |
| `timeout MS` | Tiempo de espera de respuesta (por defecto 5000 ms). | `timeout 1000` |
| `cooked` | Formatea ("cocina") la salida de variables a forma legible. | `cooked` |
| `raw` | Muestra las respuestas tal cual las envía el servidor. | `raw` |
| `drefid [hash\|ipv4]` | Formato de presentación del *refid* en `peers`. | `drefid ipv4` |
| `debug [more\|less\|off]` | Ajusta la verbosidad de depuración en sesión. | `debug more` |
| `version` | Versión del binario `ntpq`. | `version` |
| `quit`, `exit` | Sale de la sesión interactiva. | `quit` |

---

# Asociaciones y peers

Una *asociación* es la relación de `ntpd` con cada fuente; cada una tiene un `assocID` numérico que muchos comandos requieren.

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `peers` | Resumen de peers: tally, remoto, refid, st, t, when, poll, reach, delay, offset, jitter. | `peers` |
| `apeers` | Como `peers` pero añade el `assocID` y el refid extendido. | `apeers` |
| `opeers` | Formato antiguo: muestra la dirección local de destino en vez del refid. | `opeers` |
| `lpeers` | Lista todas las asociaciones, incluidas las de clientes/estáticas. | `lpeers` |
| `associations` | Tabla de asociaciones: índice, assocID, status, conf, reach, auth, condition. | `associations` |
| `lassociations` | Como `associations` pero incluye asociaciones no configuradas. | `lassociations` |
| `passociations` | Imprime los datos de asociación **almacenados** (sin consultar de nuevo). | `passociations` |
| `lpassociations` | Versión "long" de `passociations` (todas las asociaciones cacheadas). | `lpassociations` |
| `pstatus ASSOCID` | Estado detallado (status word) de una asociación concreta. | `pstatus 31042` |

---

# Lectura y escritura de variables

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `readvar [ASSOCID] [var...]`, `rv` | Lee variables; con `0` o sin assocID lee las **variables del sistema**. | `rv 0 offset,sys_jitter` |
| `readlist [ASSOCID]`, `rl` | Lee la lista de variables estándar de una asociación. | `rl 31042` |
| `writevar ASSOCID var=val`, `wv` | Escribe variables en una asociación (requiere autenticación). | `wv 31042 leap=0` |
| `writelist`, `wl` | Escribe la lista interna de variables. | `wl` |
| `mreadvar A1 A2 [var...]`, `mrv` | Lee variables en un rango de asociaciones. | `mrv 31042 31048 offset` |
| `mreadlist A1 A2`, `mrl` | Lee la lista estándar en un rango de asociaciones. | `mrl 31042 31048` |
| `clockvar [ASSOCID]`, `cv` | Variables de un reloj de referencia (refclock). | `cv 31040` |

---

# Estado y estadísticas del demonio

Lecturas globales del estado operativo de `ntpd` (la mayoría existen desde ntp 4.2.8).

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `sysinfo` | Resumen del sistema: peer asociado, stratum, precisión, root delay/dispersion, leap. | `sysinfo` |
| `sysstats` | Contadores de paquetes desde el arranque (recibidos, procesados, descartados…). | `sysstats` |
| `monstats` | Estadísticas del subsistema de monitorización (MRU). | `monstats` |
| `iostats` | Estadísticas de E/S de red (paquetes/bytes enviados y recibidos, errores). | `iostats` |
| `timerstats` | Estadísticas del temporizador interno. | `timerstats` |
| `authinfo` | Estadísticas del subsistema de autenticación (claves, fallos de MAC). | `authinfo` |
| `kerninfo` | Estado de la disciplina de reloj del kernel (PLL, estado, offset). | `kerninfo` |
| `ifstats` | Lista de interfaces de red en uso por `ntpd` (requiere autenticación). | `ifstats` |
| `mrulist` | Tabla MRU: hosts recientes que han contactado al servidor. | `mrulist` |
| `reslist` | Lista de entradas de restricción (`restrict`) activas (requiere autenticación). | `reslist` |

---

# Configuración en caliente (runtime)

Requieren `controlkey` configurado en `ntp.conf` y autenticación con `keyid`/`passwd`.

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `config "LÍNEA"` | Inyecta una línea de configuración como si estuviera en `ntp.conf`, sin reiniciar. | `config "server 10.0.0.1 iburst"` |
| `config-from-file FICHERO` | Aplica directivas desde un fichero de texto. | `config-from-file /tmp/extra.conf` |
| `saveconfig FICHERO` | Vuelca la configuración en ejecución a un fichero (en `saveconfigdir`). | `saveconfig running.conf` |

---

# Referencia: códigos *tally* y *reach*

El primer carácter de cada línea de `peers` indica el estado de selección de la fuente:

| Símbolo | Significado |
|---------|-------------|
| ` ` (espacio) | Descartada: alcanzable pero alto stratum/distancia o fallo de cordura. |
| `x` | Descartada por el algoritmo de *intersección* (falseticker). |
| `.` | Excluida del final de la lista de candidatos (demasiadas fuentes). |
| `-` | Descartada por el algoritmo de *clustering*. |
| `+` | Candidato válido, incluido en el cómputo final (combine). |
| `#` | Buena fuente pero no entre las 6 mejores por distancia de sincronización. |
| `*` | **System peer**: la fuente actualmente seleccionada para disciplinar el reloj. |
| `o` | System peer designado por pulso PPS (sincronización por reloj de pulsos). |

> El campo **`reach`** es un registro de desplazamiento octal de 8 bits: `377` (binario `11111111`) indica que las últimas 8 sondas tuvieron respuesta. Valores como `001`, `017`, `077` reflejan un peer que empieza a responder o pierde paquetes. `when` es segundos desde la última respuesta; `poll` es el intervalo de sondeo en segundos.

---

# Ejemplos de diagnóstico

| Objetivo | Comando |
|----------|---------|
| Ver peers sin resolver DNS, en una sola pasada. | `ntpq -pn` |
| Comprobar offset y jitter del sistema. | `ntpq -c 'rv 0 offset,sys_jitter,frequency'` |
| Listar peers de un servidor remoto. | `ntpq -pn ntp1.example.net` |
| Encadenar resumen del sistema + peers. | `ntpq -c sysinfo -c peers` |
| Identificar quién consulta al servidor (abuso/monitorización). | `ntpq -c mrulist` |
| Detectar falsetickers (marcados con `x`). | `ntpq -pn | awk '/^x/'` |
