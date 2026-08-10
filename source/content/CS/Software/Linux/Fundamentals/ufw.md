**UFW** (*Uncomplicated Firewall*) es una interfaz de línea de comandos para gestionar un firewall netfilter en Linux, pensada para simplificar la administración frente a `iptables`/`ip6tables` directos. Traduce reglas de sintaxis sencilla o completa a cadenas de netfilter y las persiste en `/etc/ufw`. En la instalación queda deshabilitado con política de entrada `deny`, reenvío `deny` y salida `allow`. No pretende cubrir toda la funcionalidad de netfilter, sino añadir/quitar reglas simples de forma rápida; casi todas las órdenes requieren `root` (`sudo`).

```
ufw [--dry-run] [--force] COMANDO [ARGS]
```

---

## Opciones globales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--version` | Muestra la versión del programa y termina. | `ufw --version` |
| `-h` / `--help` | Muestra el mensaje de ayuda y termina. | `ufw --help` |
| `--dry-run` | No modifica nada: muestra los cambios que se aplicarían. Ideal para validar reglas antes de comprometerlas. | `sudo ufw --dry-run allow 80/tcp` |
| `--force` | Ejecuta sin pedir confirmación interactiva (útil en scripts para `reset`, `delete NUM`, `enable`). | `sudo ufw --force reset` |

---

## Estado del firewall

Subcomandos que controlan el ciclo de vida del firewall.

| Comando | Descripción | Ejemplo |
|--------|-------------|---------|
| `enable` | Recarga el firewall y lo activa en el arranque. | `sudo ufw enable` |
| `disable` | Descarga el firewall y lo desactiva en el arranque. | `sudo ufw disable` |
| `reload` | Recarga el firewall aplicando la configuración actual. | `sudo ufw reload` |
| `reset` | Deshabilita y restaura el firewall a los valores de instalación. Hace copia de seguridad de las reglas actuales. Admite `--force`. | `sudo ufw reset` |
| `status` | Muestra el estado del firewall y las reglas gestionadas por ufw. | `sudo ufw status` |
| `status verbose` | Estado con información extra (logging, políticas por defecto, perfil). | `sudo ufw status verbose` |
| `status numbered` | Lista las reglas numeradas (los números se usan con `delete NUM` / `insert NUM`). | `sudo ufw status numbered` |

> `status` no muestra las reglas de los ficheros de `/etc/ufw`. Para el estado completo del firewall en crudo usa `ufw show raw`.

---

## Políticas por defecto

| Comando | Descripción | Ejemplo |
|--------|-------------|---------|
| `default allow\|deny\|reject DIRECCIÓN` | Cambia la política por defecto para el tráfico en `DIRECCIÓN`, donde `DIRECCIÓN` es `incoming`, `outgoing` o `routed`. Las reglas existentes deben migrarse manualmente al cambiarla. | `sudo ufw default deny incoming` |

Configuración de endurecimiento típica: denegar todo lo entrante y permitir lo saliente, añadiendo después reglas `allow` específicas.

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw default deny routed
```

---

## Registro (logging)

| Comando | Descripción | Ejemplo |
|--------|-------------|---------|
| `logging on` | Activa el registro a nivel `low` si está desactivado. | `sudo ufw logging on` |
| `logging off` | Desactiva el registro gestionado por ufw. | `sudo ufw logging off` |
| `logging LEVEL` | Fija el nivel de registro. Los paquetes registrados usan la *facility* syslog `LOG_KERN` (también `/var/log/ufw.log` con rsyslog). | `sudo ufw logging medium` |

**Niveles (`LEVEL`):**

| Nivel | Qué registra |
|-------|--------------|
| `off` | Desactiva el registro gestionado por ufw. |
| `low` | Paquetes bloqueados que no cumplen la política, más los que casan reglas con `log` (con rate limiting). Nivel por defecto. |
| `medium` | Nivel `low` + paquetes permitidos que no casan política, todos los `INVALID` y todas las conexiones nuevas (con rate limiting). |
| `high` | Nivel `medium` sin rate limiting, más todos los paquetes (con rate limiting). |
| `full` | Nivel `high` sin rate limiting. |

> Niveles por encima de `medium` generan mucho volumen y pueden llenar el disco.

---

## Reglas: allow / deny / reject / limit

Cuatro subcomandos añaden reglas que difieren solo en la acción. Comparten la misma [sintaxis de reglas](#sintaxis-de-reglas).

| Comando | Acción | Ejemplo |
|--------|--------|---------|
| `allow ARGS` | Permite el tráfico que casa la regla. | `sudo ufw allow 22/tcp` |
| `deny ARGS` | Descarta silenciosamente (DROP) el tráfico. | `sudo ufw deny 23` |
| `reject ARGS` | Rechaza el tráfico devolviendo error al emisor (ICMP/RST) en vez de ignorarlo. | `sudo ufw reject out smtp` |
| `limit ARGS` | Permite la conexión pero la deniega si una IP inicia **6 o más conexiones en 30 segundos**. Protege frente a fuerza bruta. | `sudo ufw limit ssh/tcp` |

---

## Sintaxis de reglas

Existen dos sintaxis: **simple** (solo puerto y opcionalmente protocolo, aplicada al host) y **completa** (basada libremente en la sintaxis de PF de OpenBSD, con origen y destino).

**Simple** — `ufw ACCIÓN [in|out] [log|log-all] PUERTO[/PROTOCOLO] | NOMBRE_SERVICIO [comment '...']`

**Completa** — `ufw [route] [delete] [insert NUM] [prepend] ACCIÓN [in|out [on IFAZ]] [log|log-all] [proto PROTO] [from DIRECCIÓN [port PUERTO | app APP]] [to DIRECCIÓN [port PUERTO | app APP]] [comment '...']`

| Elemento | Descripción | Ejemplo |
|--------|-------------|---------|
| `PUERTO[/PROTOCOLO]` | Puerto (o servicio de `/etc/services`) y protocolo opcional (`tcp`/`udp`). Sin protocolo aplica a ambos. | `sudo ufw allow 25/tcp` |
| `NOMBRE_SERVICIO` | Nombre de servicio resuelto por `/etc/services`. | `sudo ufw allow smtp` |
| `in` / `out` | Dirección: tráfico entrante o saliente. Sin especificar, aplica a entrante. | `sudo ufw reject out smtp` |
| `on IFAZ` | Restringe la regla a una interfaz concreta. | `sudo ufw allow in on eth0 to any port 80 proto tcp` |
| `proto PROTO` | Protocolo (ver tabla de protocolos). | `sudo ufw deny proto tcp to any port 80` |
| `from DIRECCIÓN` | Origen: IP/CIDR IPv4 o IPv6, o `any`. Admite `port` y `app`. | `sudo ufw allow from 192.168.1.0/24` |
| `to DIRECCIÓN` | Destino: IP/CIDR o `any`. Admite `port` y `app`. | `sudo ufw deny proto tcp from 10.0.0.0/8 to 192.168.0.1 port 25` |
| `port PUERTO` | Puerto asociado a `from`/`to`. Rangos con `:` y listas con `,`. | `sudo ufw allow proto tcp to any port 80,443,8080:8090` |
| `app APP` | Usa un perfil de aplicación en lugar de puerto. | `sudo ufw allow from 192.168.0.0/16 to any app 'OpenSSH'` |
| `log` | Registra las conexiones **nuevas** que casan la regla. | `sudo ufw allow log 22/tcp` |
| `log-all` | Registra **todos** los paquetes que casan la regla. | `sudo ufw allow log-all 22/tcp` |
| `comment '...'` | Añade/actualiza un comentario. Comentario `''` lo elimina. `insert`/`prepend` no pueden actualizar comentarios. | `sudo ufw allow 22/tcp comment 'SSH admin'` |

**Puertos múltiples:** la lista debe ser numérica, sin espacios, y se modifica como un todo (no se puede borrar luego un solo puerto). Máximo 15 puertos; los rangos cuentan como 2.

---

## Protocolos soportados

Válidos en cualquier regla y habilitados cuando no se especifica protocolo: `tcp`, `udp`.

Con restricciones (solo en sintaxis **completa**, algunos válidos sin puerto):

| Protocolo | Restricción |
|-----------|-------------|
| `ah` | Válido sin número de puerto (IPSec AH, nº 51). |
| `esp` | Válido sin número de puerto (IPSec ESP, nº 50). |
| `gre` | Válido sin número de puerto. |
| `vrrp` | Válido sin número de puerto (keepalived, nº 112). |
| `ipv6` | Túneles IPv6-sobre-IPv4 / 6to4 (nº 41). Válido para direcciones IPv4 y sin puerto. |
| `igmp` | Válido para direcciones IPv4 y sin puerto. |

```bash
sudo ufw allow to 10.0.0.1 proto esp
sudo ufw allow in on eth0 to 224.0.0.1 proto igmp
sudo ufw allow to 10.0.0.1 from 10.4.0.0/16 proto ipv6
```

---

## Gestión de reglas (delete / insert / prepend)

| Comando | Descripción | Ejemplo |
|--------|-------------|---------|
| `delete RULE` | Borra la regla prefijando la regla original con `delete` (con o sin comentario). En reglas genéricas IPv4+IPv6 borra ambas. | `sudo ufw delete deny 80/tcp` |
| `delete NUM` | Borra la regla por número (ver `status numbered`). En una regla IPv4+IPv6 borra solo la variante indicada. Admite `--force`. | `sudo ufw delete 3` |
| `insert NUM RULE` | Inserta la regla en la posición `NUM`. | `sudo ufw insert 3 deny to any port 22 from 10.0.0.135 proto tcp` |
| `prepend RULE` | Antepone la regla al inicio del conjunto (antes de las de su tipo IP). Útil en firewalls dinámicos/IPS. | `sudo ufw prepend deny from 1.2.3.4` |

> El orden importa: **la primera coincidencia gana**. Añade primero las reglas específicas y después las generales.

---

## Reglas de enrutamiento (route)

Reglas para el tráfico que **atraviesa** el firewall (cadena FORWARD de netfilter), no dirigido al propio host. Requieren activar el reenvío IP en `/etc/ufw/sysctl.conf`.

| Comando | Descripción | Ejemplo |
|--------|-------------|---------|
| `route RULE` | Añade una regla de enrutamiento (`allow`/`deny`/`reject`/`limit`) con la sintaxis completa. | `sudo ufw route allow in on eth1 out on eth2` |
| `route delete RULE\|NUM` | Borra una regla de enrutamiento por regla o por número. | `sudo ufw route delete allow in on eth1 out on eth2` |
| `route insert NUM RULE` | Inserta una regla de enrutamiento en la posición `NUM`. | `sudo ufw route insert 1 allow in on eth0 out on eth1 to 12.34.45.67 port 80 proto tcp` |

Habilitar reenvío (luego `sudo ufw disable && sudo ufw enable`):

```
net/ipv4/ip_forward=1
net/ipv6/conf/default/forwarding=1
net/ipv6/conf/all/forwarding=1
```

---

## Informes (show)

`ufw show REPORT` muestra información del firewall en ejecución. Salvo `listening`, los informes se dan en formato `iptables` crudo.

| Informe | Descripción | Ejemplo |
|--------|-------------|---------|
| `raw` | Firewall completo (tablas `filter`, `nat`, `mangle`, `raw`). | `sudo ufw show raw` |
| `builtins` | Reglas de las cadenas internas de netfilter. | `sudo ufw show builtins` |
| `before-rules` | Reglas evaluadas antes de las del usuario. | `sudo ufw show before-rules` |
| `user-rules` | Reglas añadidas por el usuario. | `sudo ufw show user-rules` |
| `after-rules` | Reglas evaluadas después de las del usuario. | `sudo ufw show after-rules` |
| `logging-rules` | Reglas relacionadas con el registro. | `sudo ufw show logging-rules` |
| `listening` | Puertos en escucha (tcp) / abiertos (udp) del sistema, con interfaz, ejecutable y reglas que les afectan. | `sudo ufw show listening` |
| `added` | Reglas tal y como se añadieron por línea de comandos (normalizadas; no refleja el estado en ejecución). | `sudo ufw show added` |

---

## Perfiles de aplicación (app)

Los perfiles definen conjuntos de puertos con nombre en `/etc/ufw/applications.d`.

| Comando | Descripción | Ejemplo |
|--------|-------------|---------|
| `app list` | Lista los perfiles de aplicación disponibles. | `sudo ufw app list` |
| `app info PERFIL` | Muestra la información (puertos/protocolos) de un perfil. | `sudo ufw app info OpenSSH` |
| `app update PERFIL` | Actualiza el perfil (recarga su definición). `app update all` para todos. | `sudo ufw app update OpenSSH` |
| `app default ARG` | Fija la política por defecto de aplicaciones. | `sudo ufw app default skip` |

Uso de un perfil en una regla:

```bash
sudo ufw allow OpenSSH
sudo ufw allow from 192.168.1.0/24 to any app 'Nginx Full'
```

---

## Casos de uso comunes

```bash
# Endurecimiento base: denegar entrante, permitir saliente, y SSH con rate limit
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw limit 22/tcp comment 'SSH brute-force protect'
sudo ufw enable

# Servicios web
sudo ufw allow proto tcp from any to any port 80,443 comment 'web app'

# Permitir SSH solo desde una red de gestión
sudo ufw allow from 10.10.0.0/16 to any port 22 proto tcp

# Rechazar (con aviso) un servicio saliente
sudo ufw reject out 25/tcp comment 'no SMTP saliente'

# Bloquear una IP concreta con máxima prioridad
sudo ufw prepend deny from 1.2.3.4

# Ver reglas numeradas y borrar la nº 4
sudo ufw status numbered
sudo ufw delete 4

# Validar una regla sin aplicarla
sudo ufw --dry-run allow 8080/tcp

# Estado detallado y puertos en escucha
sudo ufw status verbose
sudo ufw show listening

# Reset no interactivo (scripts)
sudo ufw --force reset
```

---

## Notas

- IPv6 está permitido por defecto; se controla con `IPV6=yes|no` en `/etc/default/ufw`. Una regla genérica (`ufw allow 22`) aplica a IPv4 e IPv6.
- Reglas base tras la instalación: DROP de cabeceras RH0 e `INVALID`, ACCEPT de ciertos ICMP/ICMPv6, DHCP, mDNS y UPnP; DROP de tráfico no local.
- Con política por defecto `REJECT`, ufw puede interferir con reglas añadidas fuera de su marco.
- Para personalización avanzada de netfilter, ver `ufw-framework(8)`. Relacionado: [[Hardening]], [[Firewall]], [[sudo]].
- **Ver también:** `iptables(8)`, `ip6tables(8)`, `sysctl(8)`.
