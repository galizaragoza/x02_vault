| **Parámetro/Comando**                  | **Función**                                            | **Ejemplo de Sintaxis**                        |
| ---------------------------------- | -------------------------------------------------- | ------------------------------------------ |
| Sin parámetros                     | Muestra todas las interfaces activas               | `ifconfig`                                 |
| `[interfaz]`                       | Muestra configuración de interfaz específica       | `ifconfig eth0`                            |
| `[interfaz] up`                    | Activa una interfaz de red                         | `ifconfig eth0 up`                         |
| `[interfaz] down`                  | Desactiva una interfaz de red                      | `ifconfig eth0 down`                       |
| `[interfaz] [ip]`                  | Asigna dirección IP a interfaz                     | `ifconfig eth0 192.168.1.100`              |
| `[interfaz] netmask [máscara]`     | Configura máscara de red                           | `ifconfig eth0 netmask 255.255.255.0`      |
| `[interfaz] broadcast [dirección]` | Configura dirección broadcast                      | `ifconfig eth0 broadcast 192.168.1.255`    |
| `[interfaz] mtu [valor]`           | Configura MTU (Maximum Transmission Unit)          | `ifconfig eth0 mtu 1500`                   |
| `-a`                               | Muestra TODAS las interfaces (activas e inactivas) | `ifconfig -a`                              |
| `[interfaz] hw ether [mac]`        | Cambia dirección MAC de interfaz                   | `ifconfig eth0 hw ether 00:11:22:33:44:55` |
| `[interfaz]:[alias] [ip]`          | Crea alias de interfaz (IP adicional)              | `ifconfig eth0:0 192.168.1.200`            |
