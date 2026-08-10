# Diagnóstico de red, conectividad y puertos

#Linux #Networking #Monitoring #Troubleshooting

Guía de refresco rápido para evaluar y reparar el estado de la red en un host Linux: interfaces y direccionamiento, conectividad capa por capa (L2→L3→DNS→L4→L7), **diagnóstico y mantenimiento DNS** (resolver, caché, DoT/DoH), puertos y sockets en escucha, qué proceso usa cada puerto, latencia/pérdida, captura de tráfico y **mantenimiento** (renovar DHCP, purgar cachés, MTU, config persistente). El enfoque es **subir por la pila OSI en orden**: si falla abajo, no tiene sentido depurar arriba.

> Método: enlace (`ip link`) → IP/ruta (`ip addr`, `ip route`) → vecino/gateway (`ping`, `ip neigh`) → DNS (`dig`) → transporte (`ss`, `nc`) → aplicación (`curl`). Aislar la capa antes de tocar nada.

---

## 1. Interfaces, direcciones y rutas (L2/L3)

Herramienta moderna: **`iproute2`** (`ip`, `ss`). `ifconfig`/`netstat`/`route` están obsoletos pero aún presentes.

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `ip -br -c a` | Resumen breve y coloreado de interfaces e IPs. | `ip -br -c addr` |
| `ip addr` | Direcciones IPv4/IPv6 por interfaz. | `ip addr show dev eth0` |
| `ip link` | Estado del enlace (UP/DOWN, MAC, MTU). | `ip link show` |
| `ip route` | Tabla de rutas y gateway por defecto. | `ip route get 8.8.8.8` |
| `ip neigh` | Tabla ARP/NDP (vecinos L2 resueltos). | `ip neigh show` |
| `ethtool eth0` | Velocidad negociada, duplex, link detectado (capa física). | `ethtool eth0` |
| `ethtool -S eth0` | Contadores del driver (errores, drops, overruns). | `ethtool -S eth0` |
| `nmcli device status` | Estado de conexiones gestionadas por NetworkManager. | `nmcli dev status` |

Ver [[ethtool]] · [[ifconfig]] · [[nmcli]].

```bash
# ¿La interfaz tiene link físico y la negociación es correcta?
ip link show eth0            # estado LOWER_UP = cable/enlace presente
ethtool eth0 | grep -E 'Speed|Duplex|Link detected'
# ¿Hay gateway por defecto?
ip route | grep default
```

---

## 2. Conectividad: ¿llega el paquete? (ICMP/ruta)

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `ping -c4 host` | Alcanzabilidad y RTT básico (ICMP echo). | `ping -c4 1.1.1.1` |
| `ping -c4 -I eth0 host` | Forzar interfaz de salida. | `ping -c4 -I eth0 10.0.0.1` |
| `traceroute host` | Salto a salto hasta el destino (UDP por def.). | `traceroute 8.8.8.8` |
| `traceroute -T -p 443 host` | Traceroute TCP a un puerto (atraviesa firewalls que filtran UDP/ICMP). | `traceroute -T -p 443 example.com` |
| `mtr host` | `ping`+`traceroute` continuo: pérdida y latencia por salto en vivo. | `mtr -rwc 20 8.8.8.8` |
| `ip route get IP` | Qué ruta/interfaz/origen usaría el kernel para esa IP. | `ip route get 8.8.8.8` |

Ver [[ping]] · [[traceroute]].

> Distinguir: ping a la **IP** funciona pero a un **nombre** no → problema de **DNS** (sección 3), no de red. Pérdida solo en saltos intermedios de `mtr` pero 0% en el destino → suele ser rate-limit de ICMP en routers, no pérdida real.

---

## 3. Resolución DNS: diagnóstico y mantenimiento (capa de nombres)

Teoría, tipos de registro y jerarquía → [[DNS 101]] · cheatsheet [[DNS_CS]]. Aquí solo el **diagnóstico operativo**: comprobar resolución, aislar el resolver culpable, inspeccionar/purgar caché y verificar canal cifrado.

### 3.1 Consulta rápida (¿resuelve?)

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `dig dominio` | Consulta completa (A por def.) con servidor, flags y tiempos. | `dig example.com` |
| `dig +short dominio` | Solo la respuesta (apto para scripts). | `dig +short example.com` |
| `dig +noall +answer dominio` | Solo la sección ANSWER, formateada. | `dig +noall +answer example.com` |
| `dig @resolver dominio` | Forzar un resolver concreto (descartar el local). | `dig @1.1.1.1 example.com` |
| `dig dominio MX`/`TXT`/`AAAA`/`NS`/`SOA` | Otros tipos de registro. | `dig example.com MX` |
| `host -a dominio` | Volcado breve de todos los registros (alternativa a `dig`). | `host -a example.com` |
| `nslookup -type=mx dominio servidor` | Equivalente interactivo/portable (también en Windows). | `nslookup -type=mx example.com 1.1.1.1` |
| `getent hosts dominio` | Resolución vía **NSS** (respeta `/etc/hosts` y `nsswitch.conf`). | `getent hosts example.com` |
| `resolvectl query dominio` | Resolución vía systemd-resolved (la misma ruta que las apps). | `resolvectl query example.com` |

Ver [[dig]] · [[host]] · [[nslookup]].

> `dig` consulta DNS **directamente**; `getent`/`ping`/la mayoría de apps usan **NSS** (`/etc/hosts`, mDNS, systemd-resolved). Si `dig` resuelve pero `getent` no, el problema está en `nsswitch.conf`, `/etc/hosts` o el stub local, **no** en el DNS.

### 3.2 `dig` avanzado (aislar dónde falla)

| Comando | Para qué |
|---------|----------|
| `dig +trace dominio` | Resolución iterativa desde la raíz: depura **delegaciones** (qué NS rompe la cadena). |
| `dig +norecurse @resolver dominio` | Pregunta sin recursión (RD=0): si hay ANSWER, está **en la caché** del resolver. |
| `dig -x IP` | PTR / DNS inverso (`IP → nombre`). |
| `dig +dnssec dominio` | Pide registros DNSSEC (RRSIG). Flag **`ad`** en la respuesta = validado por el resolver. |
| `dig @ns-autoritativo dominio SOA +short` | Lee el **serial** de zona directo del autoritativo. |
| `dig +nssearch dominio` | Consulta el SOA en **cada** NS autoritativo: detecta zonas desincronizadas. |
| `dig CH TXT version.bind @servidor` | Versión del software del servidor (clase CHAOS; útil en recon). |

```bash
# ¿El resolver responde lo mismo que el autoritativo? (caché vieja / propagación)
dig +short example.com @1.1.1.1            # vía resolver recursivo
dig +short example.com @ns1.example.com    # directo al autoritativo
# Comparar el serial SOA entre todos los NS (deben coincidir)
dig +nssearch example.com
```

> Resolución lenta: en la salida de `dig` mira **`Query time`** y el campo **`SERVER`**. Si un resolver tarda y otro (`@1.1.1.1`) va rápido, el problema es el resolver local, no la red. `+trace` revela si el retardo está en un nivel de delegación concreto.

### 3.3 ¿Qué resolver se usa realmente?

| Comando | Para qué |
|---------|----------|
| `resolvectl status` | Resolvers DNS efectivos **por interfaz** + dominios de búsqueda + modo DNSSEC (systemd-resolved). |
| `cat /etc/resolv.conf` | Resolver(es) y `search`. Suele ser un *symlink* gestionado. |
| `readlink -f /etc/resolv.conf` | Revela quién lo gestiona: stub `127.0.0.53` (resolved), NetworkManager, etc. |
| `nmcli dev show eth0 \| grep DNS` | DNS que NetworkManager aplicó a la interfaz. |
| `grep hosts /etc/nsswitch.conf` | Orden de resolución de NSS (`files dns mdns_minimal`…). |

> `/etc/resolv.conf` apuntando a `127.0.0.53` significa que las consultas pasan por el **stub de systemd-resolved**; el resolver real está en `resolvectl status`, no en ese fichero. Editar `resolv.conf` a mano no sirve si lo regenera resolved/NetworkManager.

### 3.4 Caché DNS: inspección y purga (mantenimiento)

Un cambio de IP que "no se ve" suele ser **caché con TTL aún vivo**. Purgar según el cacheador local:

| Cacheador | Inspeccionar | Purgar |
|-----------|--------------|--------|
| **systemd-resolved** | `resolvectl statistics` (hits/misses, tamaño) | `sudo resolvectl flush-caches` |
| **nscd** | `nscd -g` (estadísticas) | `sudo nscd -i hosts` (o reiniciar el servicio) |
| **dnsmasq** | logs (`journalctl -u dnsmasq`) | `sudo systemctl restart dnsmasq` · o `sudo kill -HUP $(pidof dnsmasq)` |
| **unbound** | `unbound-control stats` | `sudo unbound-control flush_zone .` / `flush dominio` |

```bash
# Verificar que la purga surtió efecto: el TTL vuelve a su valor máximo
dig +noall +answer example.com    # TTL alto = recién resuelto; TTL bajando = sirviendo de caché
```

> No existe "una" caché DNS en Linux: depende de qué corra en local. Si nada de lo anterior está instalado, el cliente **no cachea** (cada app resuelve por su cuenta) y solo cachea el resolver upstream.

### 3.5 DNS cifrado (DoT / DoH)

| Comando | Para qué |
|---------|----------|
| `nc -zv 1.1.1.1 853` | ¿Alcanzable el puerto **853** (DoT)? Si está filtrado, DoT cae a texto plano o falla. |
| `kdig -d @1.1.1.1 +tls example.com` | Consulta **DoT** explícita (knot-dnsutils); `-d` muestra el handshake TLS. |
| `kdig @1.1.1.1 +https example.com` | Consulta **DoH** (HTTPS). `dig` ≥9.18 también soporta `+tls`/`+https`. |
| `curl -s 'https://1.1.1.1/dns-query?name=example.com&type=A' -H 'accept: application/dns-json'` | DoH por API JSON (sin herramientas DNS). |
| `resolvectl query --cache=no example.com` | Resolver saltándose la caché para comparar. |

> Si las apps fallan pero `dig @8.8.8.8` (puerto 53 plano) funciona, sospechar de **DoT/DoH mal configurado** o del puerto 853 bloqueado por el firewall corporativo.

### 3.6 Árbol de decisión rápido

```
dig +short A no responde?
├─ dig @1.1.1.1 SÍ responde  → resolver local roto/caído  → §3.3 / §3.4
├─ dig @1.1.1.1 tampoco      → ¿hay ruta a internet?      → §2 (ping IP)
└─ dig OK pero la app falla  → NSS / /etc/hosts / DoT      → §3.3 / §3.5
dig resuelve IP vieja?       → caché con TTL vivo          → §3.4 (flush)
dig +trace corta en un nivel → delegación NS rota          → revisar registrar/zona
```

---

## 4. Sockets y puertos (L4)

**`ss`** sustituye a `netstat` (más rápido, lee netlink). Ver [[ss]] · [[netstat]].

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `ss -tlnp` | Puertos **TCP** en escucha (`-l`), numérico (`-n`), con proceso (`-p`). | `sudo ss -tlnp` |
| `ss -ulnp` | Puertos **UDP** en escucha. | `sudo ss -ulnp` |
| `ss -tunap` | TCP+UDP, todas las conexiones, con PID. | `sudo ss -tunap` |
| `ss -tn state established` | Solo conexiones establecidas. | `ss -tn state established` |
| `ss -s` | Resumen de sockets por estado/protocolo. | `ss -s` |
| `ss -tnp dst :443` | Conexiones a un puerto/destino concreto. | `ss -tnp dst :443` |
| `ss -tin` | Info TCP interna (rtt, cwnd, retransmisiones). | `ss -tin dst :443` |
| `lsof -i :PORT` | Qué proceso usa un puerto. | `sudo lsof -i :8080` |
| `fuser PORT/tcp` | PID que ocupa el puerto (alternativa). | `sudo fuser 8080/tcp` |

```bash
# ¿Quién escucha en el puerto 80?
sudo ss -tlnp 'sport = :80'
# ¿Cuántas conexiones por estado? (detectar fugas de TIME-WAIT/CLOSE-WAIT)
ss -tan | awk 'NR>1{c[$1]++} END{for(s in c) print s, c[s]}'
```

> Muchos **CLOSE-WAIT** = la aplicación no cierra sus sockets (bug de la app). Muchos **TIME-WAIT** = normal con alta tasa de conexiones cortas; ajustar reuse antes que `tcp_tw_*`.

---

## 5. Probar un servicio remoto (L4/L7)

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `nc -zv host PORT` | ¿Puerto TCP abierto? (escaneo puntual sin enviar datos). | `nc -zv example.com 443` |
| `nc -zvu host PORT` | Igual para UDP (menos fiable). | `nc -zvu host 53` |
| `curl -v https://host` | Petición HTTP(S) completa con handshake y cabeceras. | `curl -vI https://example.com` |
| `curl --resolve host:443:IP ...` | Forzar IP destino (saltar DNS) para aislar capas. | `curl --resolve ex.com:443:1.2.3.4 https://ex.com` |
| `curl -w '@-' -o /dev/null -s host` | Desglose de tiempos (DNS, connect, TLS, TTFB). | ver bloque inferior |
| `openssl s_client -connect host:443` | Inspeccionar TLS/certificado. | `openssl s_client -connect ex.com:443 -servername ex.com` |
| `nmap -sT -p 1-1000 host` | Escaneo de puertos (autorizado). | `nmap -p 80,443 example.com` |

```bash
# Desglose de latencia de una petición (dónde se va el tiempo)
curl -w 'dns:%{time_namelookup} conn:%{time_connect} tls:%{time_appconnect} ttfb:%{time_starttransfer} total:%{time_total}\n' \
     -o /dev/null -s https://example.com
```

---

## 6. Tráfico en vivo y captura

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `tcpdump -ni eth0 host X and port 443` | Captura filtrada en consola. | `sudo tcpdump -ni eth0 port 53` |
| `tcpdump -w cap.pcap ...` | Guardar para Wireshark. | `sudo tcpdump -ni eth0 -w cap.pcap host 1.2.3.4` |
| `iftop -i eth0` | Ancho de banda por conexión en vivo. | `sudo iftop -i eth0` |
| `nethogs eth0` | Ancho de banda **por proceso**. | `sudo nethogs eth0` |
| `sar -n DEV 1 3` | Throughput por interfaz (rxkB/s, txkB/s) histórico. | `sar -n DEV 1 3` |
| `sar -n EDEV 1 3` | Errores/drops por interfaz. | `sar -n EDEV 1 3` |
| `sar -n TCP,ETCP 1 3` | Conexiones y errores TCP (retransmisiones). | `sar -n TCP,ETCP 1 3` |
| `ip -s link` | Contadores de paquetes/errores/drops por interfaz. | `ip -s link show eth0` |

Ver [[tcpdump]] · [[sar]].

---

## 7. Firewall y filtrado (cuando "el puerto está abierto pero no conecta")

| Comando | Para qué | Ejemplo |
|---------|----------|---------|
| `sudo nft list ruleset` | Reglas nftables activas. | `sudo nft list ruleset` |
| `sudo iptables -L -n -v` | Reglas iptables (legacy) con contadores. | `sudo iptables -S` |
| `sudo ufw status verbose` | Estado de UFW. | `sudo ufw status` |
| `firewall-cmd --list-all` | Zona activa de firewalld. | `sudo firewall-cmd --list-all` |
| `sysctl net.ipv4.ip_forward` | ¿Forwarding activado? (routing entre interfaces). | `sysctl net.ipv4.ip_forward` |

Ver [[ufw]].

> Si `ss` muestra el servicio escuchando en `127.0.0.1` en vez de `0.0.0.0`/`::`, no es el firewall: el servicio **solo acepta local**. Revisar el `bind`/`listen` de la app antes de tocar reglas.

---

## 8. Mantenimiento: recargar, renovar y reparar

Acciones correctivas una vez localizada la capa. Ordenadas de menos a más invasivas: preferir recargar una conexión antes que reiniciar todo el stack.

### 8.1 Reiniciar / recargar el stack de red

| Gestor | Recargar (no corta) | Reiniciar (corta) |
|--------|---------------------|-------------------|
| **NetworkManager** | `nmcli con up <perfil>` · `nmcli dev reapply eth0` | `sudo systemctl restart NetworkManager` |
| **systemd-networkd** | `sudo networkctl reload` · `sudo networkctl reconfigure eth0` | `sudo systemctl restart systemd-networkd` |
| **netplan** | `sudo netplan try` (revierte si pierdes acceso) | `sudo netplan apply` |
| Interfaz suelta | — | `sudo ip link set eth0 down && sudo ip link set eth0 up` |

> `nmcli dev reapply` y `networkctl reconfigure` aplican cambios de config **sin tirar** la conexión: primera opción en hosts remotos. `netplan try` es la red de seguridad si editas YAML por SSH.

### 8.2 Renovar direccionamiento (DHCP)

| Comando | Para qué |
|---------|----------|
| `sudo dhclient -r eth0 && sudo dhclient eth0` | Liberar y volver a pedir lease DHCP (cliente clásico). |
| `nmcli con up <perfil>` | Renueva el lease vía NetworkManager (re-DHCP al subir). |
| `sudo networkctl renew eth0` | Renovar lease con systemd-networkd. |
| `ip addr show eth0` | Verificar la nueva IP/lease tras renovar. |

### 8.3 Purgar estados cacheados (L2/L3)

| Comando | Qué limpia |
|---------|------------|
| `sudo ip neigh flush all` | Tabla **ARP/NDP** completa (fuerza re-resolución de vecinos). |
| `sudo ip neigh flush dev eth0` | Solo los vecinos de una interfaz. |
| `resolvectl flush-caches` | Caché **DNS** local (ver §3.4 para otros cacheadores). |
| `sudo ip -s -s link show eth0` | Releer contadores de error/drop tras una reparación. |

> El **caché de rutas** del kernel se eliminó en Linux ≥3.6: `ip route flush cache` ya no tiene efecto real. Para forzar una ruta, reescríbela (`ip route replace`), no la "flushees".

### 8.4 MTU y Path MTU Discovery

Síntoma típico: `ping` y handshake TCP funcionan, pero las transferencias grandes (HTTPS, SSH con datos, VPN) se **cuelgan** → MTU mal ajustado o PMTUD bloqueado por un firewall que descarta ICMP.

| Comando | Para qué |
|---------|----------|
| `ip link show eth0 \| grep mtu` | MTU actual de la interfaz. |
| `tracepath destino` | Descubre el **PMTU** real del camino (sin privilegios). |
| `ping -M do -s 1472 destino` | Prueba DF con payload 1472 (+28 = 1500). Si falla, baja el tamaño. |
| `sudo ip link set eth0 mtu 1400` | Fijar MTU menor (temporal; persistir en la config del gestor). |

```bash
# Buscar el MTU máximo que pasa sin fragmentar (bisección manual)
ping -M do -s 1472 -c2 8.8.8.8   # 1500 total: si "Frag needed" → bajar
ping -M do -s 1392 -c2 8.8.8.8   # 1420 total (típico en PPPoE/VPN)
```

### 8.5 Dónde persiste la configuración

| Gestor | Ficheros |
|--------|----------|
| **NetworkManager** | `/etc/NetworkManager/system-connections/*.nmconnection` (keyfiles). |
| **systemd-networkd** | `/etc/systemd/network/*.network` · `*.netdev`. |
| **netplan** | `/etc/netplan/*.yaml` (renderiza a NM o networkd). |
| **DNS** | `/etc/resolv.conf` (a menudo symlink), `/etc/systemd/resolved.conf`. |
| **Estático/manual** | `/etc/hosts`, `/etc/nsswitch.conf`. |

> Un cambio con `ip`/`nmcli ... --temporary` **no sobrevive a un reinicio**. Para que persista, edita el fichero del gestor correspondiente y recarga (§8.1). Ver [[Netplan]] · [[nmcli]] · [[dhclient]].

---

## Checklist exprés (subir por la pila)

```bash
ip -br a; ip route | grep default     # L2/L3: ¿IP y gateway?
ethtool eth0 | grep 'Link detected'   # físico: ¿hay enlace?
ping -c4 <gateway>                     # ¿llega al gateway?
ping -c4 1.1.1.1                       # ¿salida a internet (IP)?
dig +short example.com                 # DNS: ¿resuelve nombres?
sudo ss -tlnp                          # ¿escucha el servicio local?
nc -zv <host> <port>                   # ¿abierto el puerto remoto?
curl -vI https://<host>                # L7: ¿responde la app?
```

```bash
# Reparación exprés (de menos a más invasivo)
sudo resolvectl flush-caches           # DNS resuelve IP vieja
sudo ip neigh flush all                # ARP inconsistente tras cambio de red
nmcli dev reapply eth0                 # reaplicar config sin cortar
sudo dhclient -r eth0 && sudo dhclient eth0   # renovar lease DHCP
```

Relacionado: [[DNS 101]] · [[DNS_CS]] · [[CPU]] · [[ss]] · [[ping]] · [[dig]] · [[host]] · [[traceroute]] · [[tcpdump]] · [[ethtool]] · [[sar]] · [[Netplan]] · [[dhclient]]
