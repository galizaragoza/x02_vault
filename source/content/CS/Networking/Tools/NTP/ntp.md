**NTP** (Network Time Protocol) sincroniza el reloj de un sistema con fuentes de tiempo de referencia sobre UDP/123, disciplinando el oscilador local con precisión de milisegundos en redes WAN y submilisegundos en LAN. La implementación de referencia es `ntpd` (proyecto ntp.org), acompañada de un conjunto de utilidades de gestión: el propio demonio, su fichero `ntp.conf`, herramientas de puesta en hora puntual (`ntpdate`, `sntp`), gestión de claves (`ntp-keygen`), consulta del kernel (`ntptime`) y el cliente de control legado (`ntpdc`). Esta guía cubre la **gestión general** del servicio; para diagnóstico vía protocolo de control, ver [[ntpq]].

```
ntpd [opciones]          # demonio
/etc/ntp.conf            # configuración
```

> NTP organiza las fuentes por **stratum**: stratum 0 = reloj físico (GPS, atómico), stratum 1 = servidor conectado a uno, y así sucesivamente. `ntpd` ajusta el reloj **slewing** (acelerando/frenando gradualmente) salvo desviaciones grandes, donde hace **step** (salto). Véase también [[ntpq]], [[snmpwalk]].

---

# `ntpd` — opciones del demonio

| Opción | Función | Ejemplo |
|--------|---------|---------|
| `-4`, `--ipv4` | Fuerza resolución de nombres a IPv4. | `ntpd -4` |
| `-6`, `--ipv6` | Fuerza resolución de nombres a IPv6. | `ntpd -6` |
| `-a`, `--authreq` | Exige autenticación criptográfica para los peers (por defecto). | `ntpd -a` |
| `-A`, `--authnoreq` | No exige autenticación (inseguro; solo redes de confianza). | `ntpd -A` |
| `-b`, `--bcastsync` | Permite sincronizar por broadcast/multicast. | `ntpd -b` |
| `-c FILE`, `--configfile=FILE` | Usa un fichero de configuración alternativo. | `ntpd -c /etc/ntp/alt.conf` |
| `-d`, `--debug-level` | Aumenta la verbosidad de depuración (repetible); no hace fork. | `ntpd -d -d` |
| `-D N`, `--set-debug-level=N` | Fija el nivel de depuración a `N`. | `ntpd -D 5` |
| `-f FILE`, `--driftfile=FILE` | Fichero donde persiste la deriva de frecuencia del oscilador. | `ntpd -f /var/lib/ntp/drift` |
| `-g`, `--panicgate` | Permite que el **primer** ajuste supere el umbral de pánico (1000 s). | `ntpd -g` |
| `-G`, `--force-step-once` | Fuerza un step del reloj al arrancar aunque el offset sea pequeño. | `ntpd -G` |
| `-i DIR`, `--jaildir=DIR` | Hace `chroot` al directorio indicado (hardening). | `ntpd -i /var/lib/ntp` |
| `-I IFACE`, `--interface=IFACE` | Escucha solo en la interfaz/dirección indicada (repetible). | `ntpd -I eth0` |
| `-k FILE`, `--keyfile=FILE` | Fichero de claves simétricas. | `ntpd -k /etc/ntp.keys` |
| `-l FILE`, `--logfile=FILE` | Redirige el log a un fichero. | `ntpd -l /var/log/ntpd.log` |
| `-L`, `--novirtualips` | No escucha en IPs virtuales/alias. | `ntpd -L` |
| `-n`, `--nofork` | No hace fork: ejecuta en primer plano (uso con systemd/contenedores). | `ntpd -n` |
| `-N`, `--nice` | Eleva la prioridad del proceso. | `ntpd -N` |
| `-p FILE`, `--pidfile=FILE` | Escribe el PID en el fichero indicado. | `ntpd -p /run/ntpd.pid` |
| `-P N`, `--priority=N` | Fija la prioridad de scheduling del proceso. | `ntpd -P -10` |
| `-q`, `--quit` | Pone el reloj en hora una vez y sale (reemplaza a `ntpdate`). | `ntpd -gq` |
| `-r DELAY`, `--propagationdelay` | Retardo de propagación para broadcast. | `ntpd -r 0.004` |
| `-s DIR`, `--statsdir=DIR` | Directorio de los ficheros de estadísticas. | `ntpd -s /var/log/ntpstats` |
| `-t KEY`, `--trustedkey=KEY` | Añade un keyid de confianza (repetible). | `ntpd -t 1 -t 5` |
| `-u USER[:GRP]`, `--user` | Suelta privilegios ejecutándose como ese usuario/grupo. | `ntpd -u ntp:ntp` |
| `-x`, `--slew` | Nunca hace step: corrige por slewing hasta 600 s de offset. | `ntpd -x` |
| `--help` / `--version` | Ayuda / versión. | `ntpd --version` |

> Combinación habitual al arranque: `ntpd -gq` (un solo ajuste, sin importar el tamaño, y salir) para inicializar; `ntpd -g -u ntp:ntp` como servicio.

---

# `ntp.conf` — fuentes de tiempo

| Directiva | Función | Ejemplo |
|-----------|---------|---------|
| `server ADDR [opts]` | Define una fuente en modo cliente. Opts: `iburst`, `burst`, `prefer`, `minpoll N`, `maxpoll N`, `key N`. | `server 0.pool.ntp.org iburst` |
| `pool ADDR [opts]` | Igual que `server` pero expande a varias direcciones del pool DNS. | `pool 2.pool.ntp.org iburst` |
| `peer ADDR [opts]` | Asociación simétrica activa (intercambio mutuo entre pares). | `peer ntp2.lan iburst` |
| `broadcast ADDR` | Difunde el tiempo a una dirección de broadcast/multicast. | `broadcast 192.168.1.255` |
| `broadcastclient` | Acepta sincronización desde servidores broadcast. | `broadcastclient` |
| `multicastclient [ADDR]` | Acepta sincronización por multicast. | `multicastclient 224.0.1.1` |
| `manycastclient ADDR` / `manycastserver` | Descubrimiento dinámico de servidores por multicast. | `manycastclient 239.1.1.1 iburst` |
| `fudge ADDR [opts]` | Ajusta parámetros de un reloj de referencia (refclock). | `fudge 127.127.1.0 stratum 10` |

> `iburst` envía una ráfaga inicial de 6 paquetes para sincronizar en segundos en vez de minutos; recomendado en casi todas las líneas `server`/`pool`.

---

# `ntp.conf` — control de acceso (`restrict`)

`restrict ADDR [mask M] [flags]` aplica restricciones; `restrict default` fija la política base. Las reglas más específicas ganan.

| Flag | Función | Ejemplo |
|------|---------|---------|
| `ignore` | Descarta todo paquete del origen (incluido NTP). | `restrict 10.0.0.0 mask 255.0.0.0 ignore` |
| `kod` | Envía paquete *Kiss-o'-Death* al exceder el límite (con `limited`). | `restrict default kod limited` |
| `limited` | Aplica rate-limiting (`discard`) a las consultas. | `restrict default limited` |
| `nomodify` | Prohíbe comandos que cambien el estado (control mode 6/7). | `restrict default nomodify` |
| `noquery` | Bloquea consultas de control/estado (`ntpq`, `ntpdc`). | `restrict default noquery` |
| `noserve` | No sirve tiempo, solo permite control. | `restrict 10.0.0.0 noserve` |
| `nopeer` | Impide establecer asociaciones de peer no configuradas. | `restrict default nopeer` |
| `notrap` | Deniega el servicio de control-message trap. | `restrict default notrap` |
| `notrust` | Solo acepta paquetes autenticados del origen. | `restrict 10.0.0.5 notrust` |
| `version` | Descarta paquetes que no coincidan con la versión actual. | `restrict default version` |
| `mssntp` | Habilita la extensión MS-SNTP (firma para clientes AD). | `restrict 10.0.0.0 mssntp` |

> Plantilla endurecida típica: `restrict default kod nomodify notrap nopeer noquery limited` + `restrict 127.0.0.1` + `restrict ::1`.

---

# `ntp.conf` — claves y autenticación

| Directiva | Función | Ejemplo |
|-----------|---------|---------|
| `keys FILE` | Ruta del fichero de claves simétricas. | `keys /etc/ntp.keys` |
| `trustedkey N [N...]` | Lista de keyids considerados de confianza. | `trustedkey 1 5 10` |
| `controlkey N` | Keyid que autoriza comandos de control (`ntpq` runtime). | `controlkey 1` |
| `requestkey N` | Keyid que autoriza peticiones del legado `ntpdc`. | `requestkey 1` |
| `crypto` | Habilita el subsistema Autokey (PKI; **desaconsejado**, inseguro). | `crypto pw clave` |
| `enable auth` / `disable auth` | Activa/desactiva la exigencia de autenticación global. | `disable auth` |

---

# `ntp.conf` — tuning, logging y estadísticas

| Directiva | Función | Ejemplo |
|-----------|---------|---------|
| `driftfile FILE` | Persiste la deriva de frecuencia entre reinicios. | `driftfile /var/lib/ntp/drift` |
| `tos [opts]` | Parámetros del algoritmo de selección: `orphan N`, `minclock`, `maxclock`, `minsane`, `mindist`. | `tos orphan 7` |
| `tinker [opts]` | Ajusta umbrales: `panic`, `step`, `stepout`, `dispersion`, `allan`, `huffpuff`. | `tinker panic 0` |
| `discard [opts]` | Rate-limit de servicio: `average N`, `minimum N`, `monitor N`. | `discard average 4 minimum 2` |
| `logfile FILE` | Fichero de log del demonio. | `logfile /var/log/ntp.log` |
| `logconfig KEYS` | Selecciona qué clases de eventos se registran. | `logconfig =syncall +clockall` |
| `statsdir DIR` | Directorio de los ficheros de estadísticas. | `statsdir /var/log/ntpstats/` |
| `statistics TIPOS` | Activa recogida: `loopstats`, `peerstats`, `clockstats`, `rawstats`, `sysstats`. | `statistics loopstats peerstats` |
| `filegen TIPO [opts]` | Controla rotación/nombrado de los ficheros de estadística. | `filegen peerstats file peerstats type day enable` |
| `leapfile FILE` | Fichero de segundos intercalares (leap seconds). | `leapfile /etc/ntp.leapseconds` |
| `includefile FILE` | Incluye otro fichero de configuración. | `includefile /etc/ntp.d/local.conf` |
| `interface [listen\|ignore\|drop] X` | Controla en qué interfaces/direcciones opera `ntpd`. | `interface ignore wildcard` |

---

# `ntpdate` — puesta en hora puntual (legado)

Cliente one-shot que sincroniza y sale. **Obsoleto**: usar `ntpd -gq` o `sntp`. Útil aún en scripts de arranque.

| Opción | Función | Ejemplo |
|--------|---------|---------|
| `-q` | Solo consulta el offset, no ajusta el reloj (dry-run). | `ntpdate -q pool.ntp.org` |
| `-b` | Fuerza step (salto) del reloj en lugar de slew. | `ntpdate -b ntp.lan` |
| `-B` | Fuerza slew aunque el offset sea grande. | `ntpdate -B ntp.lan` |
| `-d` | Modo depuración: muestra el proceso sin tocar el reloj. | `ntpdate -d pool.ntp.org` |
| `-s` | Envía la salida a syslog en vez de stdout. | `ntpdate -s ntp.lan` |
| `-u` | Usa un puerto de origen no privilegiado (atraviesa NAT/firewall). | `ntpdate -u ntp.lan` |
| `-p N` | Número de muestras por servidor (1–8). | `ntpdate -p 4 ntp.lan` |
| `-t SEC` | Timeout por petición. | `ntpdate -t 2 ntp.lan` |
| `-o N` | Fuerza la versión NTP del paquete. | `ntpdate -o 4 ntp.lan` |
| `-a KEY` / `-k FILE` | Autentica con keyid / fichero de claves. | `ntpdate -a 1 -k /etc/ntp.keys ntp.lan` |
| `-U USER` | Ejecuta como otro usuario tras abrir el socket. | `ntpdate -U ntp ntp.lan` |

---

# `sntp` — cliente SNTP (sustituto moderno de `ntpdate`)

| Opción | Función | Ejemplo |
|--------|---------|---------|
| `-4` / `-6` | Fuerza IPv4 / IPv6. | `sntp -4 pool.ntp.org` |
| `-d`, `--debug-level` | Aumenta verbosidad de depuración. | `sntp -d ntp.lan` |
| `-S`, `--step` | Permite ajustar por step (salto). | `sntp -S ntp.lan` |
| `-s`, `--slew` | Permite ajustar por slew. | `sntp -s ntp.lan` |
| `-M MS`, `--steplimit=MS` | Umbral (ms) por debajo del cual nunca hace step. | `sntp -M 50 -S ntp.lan` |
| `-K FILE`, `--kod=FILE` | Fichero de historial Kiss-o'-Death. | `sntp -K /var/db/ntp-kod ntp.lan` |
| `-l FILE`, `--logfile=FILE` | Fichero de log. | `sntp -l /var/log/sntp.log ntp.lan` |
| `-o N`, `--ntpversion=N` | Versión NTP a usar. | `sntp -o 4 ntp.lan` |
| `-t SEC`, `--timeout=SEC` | Timeout de espera de respuesta. | `sntp -t 3 ntp.lan` |
| `-a KEY` / `-k FILE` | Autenticación con keyid / keyfile. | `sntp -a 1 -k /etc/ntp.keys ntp.lan` |

> Solo consultar offset: `sntp ntp.lan`. Consultar **y** poner en hora: `sntp -Ss ntp.lan` (requiere privilegios).

---

# `ntp-keygen` — generación de claves

Genera el fichero de claves simétricas y (legado) material Autokey.

| Opción | Función | Ejemplo |
|--------|---------|---------|
| `-M` | Genera un fichero `ntp.keys` con claves simétricas MD5/SHA. | `ntp-keygen -M` |
| `-c SCHEME` | Esquema criptográfico para Autokey (`RSA-SHA1`, `RSA-MD5`…). | `ntp-keygen -c RSA-SHA1` |
| `-p PW` | Contraseña para cifrar la clave privada generada. | `ntp-keygen -p secreto -M` |
| `-q PW` | Contraseña para descifrar una clave privada existente. | `ntp-keygen -q viejo -p nuevo` |
| `-s NAME` | Nombre/identidad del sujeto del certificado. | `ntp-keygen -s host.lan` |
| `-i IDENT` | Identidad del grupo Autokey. | `ntp-keygen -i grupoA` |
| `-l DAYS` | Tiempo de vida (días) del certificado. | `ntp-keygen -l 365` |
| `-T` | Marca el certificado como de confianza (trusted). | `ntp-keygen -T` |
| `-V N` | Genera `N` claves MV (Mu-Varadharajan). | `ntp-keygen -V 5` |

> Autokey está **desaconsejado** por debilidades conocidas; para autenticación usar claves simétricas (`-M`) y `trustedkey`/`controlkey`, o NTS sobre implementaciones modernas (chrony/ntpsec).

---

# `ntpdc` — cliente de control legado (modo 7)

Consulta/gestión por el protocolo privado mode-7. **Obsoleto y peligroso**: su comando `monlist` se usó en ataques de amplificación DDoS (CVE-2013-5211). Preferir [[ntpq]].

| Opción / Comando | Función | Ejemplo |
|------------------|---------|---------|
| `-c CMD` | Ejecuta un comando y sale. | `ntpdc -c sysinfo localhost` |
| `-n` | Direcciones en forma numérica. | `ntpdc -n -c peers` |
| `-p` | Resumen de peers. | `ntpdc -p` |
| `-l` | Lista de peers (`listpeers`). | `ntpdc -l` |
| `-s` | Información del sistema (`sysinfo`). | `ntpdc -s` |
| `-i` | Fuerza modo interactivo. | `ntpdc -i` |
| `sysinfo` / `sysstats` | Estado y estadísticas del demonio. | `ntpdc -c sysstats` |
| `monlist` | Lista de clientes recientes — **vector de amplificación, desactivar**. | `ntpdc -c monlist` |
| `kerninfo` / `loopinfo` | Estado de la disciplina kernel / bucle PLL. | `ntpdc -c loopinfo` |
| `reslist` | Lista de restricciones activas. | `ntpdc -c reslist` |

> Mitigación: `disable monitor` en `ntp.conf` (o `restrict ... noquery`) corta `monlist`. Actualizar `ntpd` ≥ 4.2.7p26.

---

# `ntptime` — interfaz con la disciplina del kernel

Lee/ajusta la disciplina de reloj del kernel (`adjtimex(2)`).

| Opción | Función | Ejemplo |
|--------|---------|---------|
| (sin args) | Muestra estado del kernel: offset, frecuencia, estimación de error, estado. | `ntptime` |
| `-c` | Muestra el tiempo de ejecución de la llamada al sistema. | `ntptime -c` |
| `-e EST` | Fija la estimación de error (µs). | `ntptime -e 1000` |
| `-f FREQ` | Fija la frecuencia (ppm). | `ntptime -f 12.5` |
| `-m MAX` | Fija el error máximo (µs). | `ntptime -m 16000` |
| `-o OFF` | Fija el offset del reloj (µs). | `ntptime -o 0` |
| `-s STATUS` | Fija el código de estado de la disciplina. | `ntptime -s 0` |
| `-T TAI` | Fija el offset TAI–UTC. | `ntptime -T 37` |

---

# Gestión del servicio (systemd) y coexistencia

`ntpd` y el `systemd-timesyncd` integrado (cliente SNTP minimalista) son **mutuamente excluyentes**: solo un disciplinador de reloj debe estar activo.

| Acción | Comando | Notas |
|--------|---------|-------|
| Habilitar/arrancar el demonio. | `systemctl enable --now ntpd` | El servicio se llama `ntpd` o `ntp` según distro. |
| Estado del servicio. | `systemctl status ntpd` | — |
| Recargar configuración. | `systemctl reload ntpd` | Relee `ntp.conf` sin cortar asociaciones. |
| Ver/forzar sincronización gestionada por systemd. | `timedatectl set-ntp true` | Activa `systemd-timesyncd`, **no** `ntpd`; desactívalo si usas `ntpd`. |
| Estado global de tiempo y RTC. | `timedatectl status` | Muestra "System clock synchronized" y servicio NTP activo. |
| Desactivar timesyncd para usar ntpd. | `timedatectl set-ntp false` + `systemctl enable --now ntpd` | Evita que dos demonios disputen el reloj. |

> Verificar tras arrancar: `ntpq -pn` (peers y tally) y `ntpq -c rv 0 offset,sys_jitter`. Ver [[ntpq]] para el diagnóstico completo. Relación con la zona horaria/RTC: [[hwclock]], [[tzselect]].
