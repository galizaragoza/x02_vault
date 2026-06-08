#reference #Software

**Netplan** = sistema de configuración de red de Ubuntu (desde 18.04). Se define en **YAML** (`/etc/netplan/*.yaml`) y Netplan lo traduce al backend real: **systemd-networkd** (servidores) o **NetworkManager** (escritorio).

# Ficheros

```
/etc/netplan/*.yaml      # configuración (se aplican en orden alfabético)
```

> YAML es **sensible a la indentación** (espacios, no tabs). Un error de indentado rompe la red.

# Ejemplo — IP estática (systemd-networkd)

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.50/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
```

# Ejemplo — DHCP

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: true
```

# Comandos

```bash
sudo netplan try        # aplicar con rollback automático si falla (seguro)
sudo netplan apply      # aplicar definitivamente
sudo netplan generate   # generar config del backend sin aplicar
netplan get             # ver config actual
sudo netplan --debug apply   # depurar
```

> `netplan try` es lo recomendado en remoto: si pierdes conectividad, revierte solo a los ~120s.

# Recursos
### [[zsh]]
### [Netplan — docs](https://netplan.io/) · [Configurar red Ubuntu (ochobits)](https://www.ochobitshacenunbyte.com/2021/04/26/netplan-configurar-la-red-en-ubuntu-20-04/)
