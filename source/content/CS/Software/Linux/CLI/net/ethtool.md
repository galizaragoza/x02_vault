ethtool consulta y configura los parámetros de una interfaz de red a nivel de driver y hardware (NIC): velocidad, dúplex, autonegociación, offloads, anillos de buffers (rings), coalescencia de interrupciones, Wake-on-LAN, estadísticas del adaptador y diagnóstico físico (cables, módulos ópticos). Opera directamente sobre el controlador del dispositivo, por lo que la mayoría de cambios requieren root y no son persistentes (se pierden al reiniciar; persistirlos vía NetworkManager, systemd-networkd o udev). Invocado solo con el nombre de interfaz muestra la configuración estándar del enlace.

```
ethtool [FLAGS] [comando] DEVNAME [opciones]
```

> Sin comando: `ethtool eth0` muestra velocidad, dúplex, modos soportados/anunciados, autonegociación y estado del enlace (`Link detected`).

---

## Flags globales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--debug MASK` | Activa mensajes de depuración (máscara de bits). | `ethtool --debug 0x1 eth0` |
| `-j` / `--json` | Salida en formato JSON (no todos los comandos). | `ethtool -j eth0` |
| `-I` / `--include-statistics` | Incluye estadísticas relacionadas con el comando. | `ethtool -I -a eth0` |
| `--version` | Muestra la versión de ethtool. | `ethtool --version` |
| `-h` / `--help` | Ayuda. | `ethtool -h` |

---

## Información y configuración del enlace

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| (sin comando) | Muestra configuración del enlace (velocidad, dúplex, autoneg, link). | `ethtool eth0` |
| `-i` / `--driver` | Muestra driver, versión de firmware y bus del dispositivo. | `ethtool -i eth0` |
| `-s` / `--change` | Cambia opciones genéricas del enlace (ver sub-opciones). | `ethtool -s eth0 speed 1000 duplex full autoneg on` |
| `-P` / `--show-permaddr` | Muestra la dirección MAC permanente (de fábrica). | `ethtool -P eth0` |
| `-r` / `--negotiate` | Reinicia la autonegociación (N-WAY). | `ethtool -r eth0` |
| `-p` / `--identify` | Hace parpadear el LED del puerto para identificarlo físicamente. | `ethtool -p eth0 10` |

### Sub-opciones de `-s` (--change)
| Sub-opción | Descripción | Ejemplo |
|------------|-------------|---------|
| `speed N` | Fija la velocidad en Mb/s. | `ethtool -s eth0 speed 1000` |
| `duplex half\|full` | Fija el modo dúplex. | `ethtool -s eth0 duplex full` |
| `autoneg on\|off` | Activa/desactiva autonegociación. | `ethtool -s eth0 autoneg off` |
| `port tp\|aui\|bnc\|mii\|fibre\|da` | Selecciona el tipo de puerto. | `ethtool -s eth0 port fibre` |
| `mdix auto\|on\|off` | Control de cruce MDI/MDI-X. | `ethtool -s eth0 mdix auto` |
| `advertise %x` | Máscara de modos a anunciar en autoneg. | `ethtool -s eth0 advertise 0x020` |
| `wol p\|u\|m\|b\|a\|g\|s\|f\|d` | Configura Wake-on-LAN (`g`=magic packet, `d`=disable). | `ethtool -s eth0 wol g` |
| `msglvl N` | Nivel de mensajes del driver. | `ethtool -s eth0 msglvl 0` |

---

## Offloads y características (features)

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `-k` / `--show-features` / `--show-offload` | Muestra el estado de los offloads de protocolo (TSO, GRO, GSO, checksums...). | `ethtool -k eth0` |
| `-K` / `--features` / `--offload` | Activa/desactiva una característica (`FEATURE on\|off`). | `ethtool -K eth0 tso off gro off` |
| `--show-priv-flags` | Muestra flags privados del driver. | `ethtool --show-priv-flags eth0` |
| `--set-priv-flags` | Configura un flag privado (`FLAG on\|off`). | `ethtool --set-priv-flags eth0 foo on` |

> Características frecuentes (`-K`): `rx`/`tx` (checksum), `tso` (TCP segmentation offload), `gso`, `gro`, `lro`, `sg` (scatter-gather), `rxhash`, `ntuple`.

---

## Anillos (rings), canales y coalescencia

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `-g` / `--show-ring` | Muestra el tamaño de los anillos RX/TX. | `ethtool -g eth0` |
| `-G` / `--set-ring` | Fija el tamaño de los anillos (`rx N`, `tx N`, `rx-jumbo N`). | `ethtool -G eth0 rx 4096 tx 4096` |
| `-l` / `--show-channels` | Muestra el nº de colas/canales (rx, tx, combined). | `ethtool -l eth0` |
| `-L` / `--set-channels` | Fija el nº de canales (`rx N`, `tx N`, `combined N`, `other N`). | `ethtool -L eth0 combined 8` |
| `-c` / `--show-coalesce` | Muestra parámetros de coalescencia de interrupciones. | `ethtool -c eth0` |
| `-C` / `--coalesce` | Fija coalescencia (`rx-usecs N`, `adaptive-rx on\|off`...). | `ethtool -C eth0 rx-usecs 50 adaptive-rx on` |
| `-Q` / `--per-queue` | Aplica un sub-comando por cola (`queue_mask %x`). | `ethtool -Q eth0 queue_mask 0x1 --coalesce rx-usecs 10` |

---

## Pausa, EEE y FEC

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `-a` / `--show-pause` | Muestra opciones de control de flujo (pause frames). | `ethtool -a eth0` |
| `-A` / `--pause` | Configura control de flujo (`autoneg`, `rx on\|off`, `tx on\|off`). | `ethtool -A eth0 rx off tx off` |
| `--show-eee` | Muestra ajustes de Energy Efficient Ethernet. | `ethtool --show-eee eth0` |
| `--set-eee` | Configura EEE (`eee on\|off`, `tx-lpi on\|off`). | `ethtool --set-eee eth0 eee off` |
| `--show-fec` | Muestra ajustes de Forward Error Correction. | `ethtool --show-fec eth0` |
| `--set-fec` | Configura FEC (`encoding auto\|off\|rs\|baser\|llrs`). | `ethtool --set-fec eth0 encoding rs` |

---

## Estadísticas y diagnóstico

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `-S` / `--statistics` | Estadísticas del adaptador (drops, errores, paquetes por cola). | `ethtool -S eth0` |
| `--phy-statistics` | Estadísticas de la capa PHY. | `ethtool --phy-statistics eth0` |
| `-t` / `--test` | Ejecuta el autotest del adaptador (`online`/`offline`). | `ethtool -t eth0 offline` |
| `-T` / `--show-time-stamping` | Capacidades de timestamping hardware (PTP). | `ethtool -T eth0` |
| `--cable-test` | Test de cable (longitud, fallos por par). | `ethtool --cable-test eth0` |
| `--cable-test-tdr` | Test de cable por reflectometría (TDR). | `ethtool --cable-test-tdr eth0` |
| `-m` / `--module-info` | Información/diagnóstico óptico del módulo SFP/QSFP (EEPROM). | `ethtool -m eth0` |
| `--show-module` | Ajustes del transceptor (módulo óptico). | `ethtool --show-module eth0` |
| `--set-module` | Configura el módulo (`power-mode-policy high\|auto`). | `ethtool --set-module eth0 power-mode-policy auto` |

---

## Flujo de hash, RSS y clasificación (NFC/ntuple)

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `-x` / `--show-rxfh` | Muestra la tabla de indirección de hash RX (RSS) y la clave hash. | `ethtool -x eth0` |
| `-X` / `--rxfh` | Configura RSS (`equal N`, `weight ...`, `hkey ...`, `hfunc ...`). | `ethtool -X eth0 equal 4` |
| `-n` / `-u` / `--show-nfc` | Muestra reglas de clasificación de flujo RX / hash por flujo. | `ethtool -n eth0 rx-flow-hash tcp4` |
| `-N` / `-U` / `--config-nfc` | Configura reglas ntuple o el hash por flujo. | `ethtool -N eth0 rx-flow-hash udp4 sdfn` |

---

## Firmware, EEPROM y registros

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `-d` / `--register-dump` | Vuelca los registros del dispositivo. | `ethtool -d eth0` |
| `-e` / `--eeprom-dump` | Vuelca la EEPROM (`offset`, `length`). | `ethtool -e eth0 offset 0 length 128` |
| `-E` / `--change-eeprom` | Modifica bytes de la EEPROM (`magic`, `offset`, `value`). | `ethtool -E eth0 magic 0x1234 offset 0 value 0x5` |
| `-f` / `--flash` | Graba firmware desde un fichero en una región del dispositivo. | `ethtool -f eth0 fw.bin` |
| `-w` / `--get-dump` | Obtiene el flag y datos de dump del driver. | `ethtool -w eth0 data dump.bin` |
| `-W` / `--set-dump` | Fija el flag de dump del dispositivo. | `ethtool -W eth0 1` |
| `--reset` | Reinicia componentes del adaptador (`mac`, `phy`, `dma`, `all`...). | `ethtool --reset eth0 all` |

---

## Sintonización (tunables y PHY)

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `--get-tunable` | Lee tunables del driver (`rx-copybreak`, `tx-copybreak`). | `ethtool --get-tunable eth0 rx-copybreak` |
| `--set-tunable` | Fija tunables del driver. | `ethtool --set-tunable eth0 rx-copybreak 256` |
| `--get-phy-tunable` | Lee tunables del PHY (`downshift`, `fast-link-down`). | `ethtool --get-phy-tunable eth0 downshift` |
| `--set-phy-tunable` | Configura tunables del PHY. | `ethtool --set-phy-tunable eth0 downshift on count 3` |
| `--monitor` | Muestra notificaciones del kernel sobre cambios de la interfaz. | `ethtool --monitor eth0` |

---

## Salida de `ethtool eth0` (campos)

| Campo | Descripción |
|-------|-------------|
| `Supported link modes` | Combinaciones velocidad/dúplex soportadas por el hardware. |
| `Advertised link modes` | Modos anunciados al otro extremo en la autonegociación. |
| `Speed` | Velocidad negociada actual (ej. `1000Mb/s`). |
| `Duplex` | Modo dúplex actual (`Full`/`Half`). |
| `Port` | Tipo de medio físico (`Twisted Pair`, `FIBRE`, `DA`). |
| `Auto-negotiation` | Estado de la autonegociación (`on`/`off`). |
| `Link detected` | Si hay enlace físico activo (`yes`/`no`). |

---

## Casos de uso comunes

```bash
# Ver velocidad, dúplex y si hay enlace
ethtool eth0

# Driver y firmware (útil para debug de hardware)
ethtool -i eth0

# Forzar 1G full sin autoneg
ethtool -s eth0 speed 1000 duplex full autoneg off

# Desactivar offloads para capturar tráfico crudo con tcpdump
ethtool -K eth0 gro off gso off tso off lro off

# Diagnóstico de errores y drops por cola
ethtool -S eth0 | grep -iE 'err|drop|discard'

# Identificar físicamente el puerto (parpadeo 10s)
ethtool -p eth0 10

# Diagnóstico óptico de un SFP+
ethtool -m eth0
```
