| **Parámetro/Comando** | **Función**                                       | **Ejemplo de Sintaxis**                            |
| ----------------- | --------------------------------------------- | ---------------------------------------------- |
| Sin parámetros    | Solicita IP via DHCP en TODAS las interfaces  | `dhclient`                                     |
| `[interfaz]`      | Solicita IP via DHCP en interfaz específica   | `dhclient eth0`                                |
| `-r`              | Libera concesión DHCP actual                  | `dhclient -r`                                  |
| `-r [interfaz]`   | Libera concesión en interfaz específica       | `dhclient -r eth0`                             |
| `-x`              | Detiene cliente DHCP (igual que -r)           | `dhclient -x`                                  |
| `-v`              | Modo verbose (muestra detalles)               | `dhclient -v eth0`                             |
| `-d`              | Fuerza ejecución en primer plano (no demonio) | `dhclient -d eth0`                             |
| `-n`              | No configura interfaz, solo prueba            | `dhclient -n eth0`                             |
| `-s [servidor]`   | Usa servidor DHCP específico                  | `dhclient -s 192.168.1.1 eth0`                 |
| `-pf [archivo]`   | Especifica archivo PID personalizado          | `dhclient -pf /var/run/dhclient.pid`           |
| `-lf [archivo]`   | Especifica archivo de concesión personalizado | `dhclient -lf /var/lib/dhcp/dhclient.leases`   |
| `-cf [archivo]`   | Usa archivo de configuración personalizado    | `dhclient -cf /etc/dhclient.conf`              |
| `-sf [script]`    | Usa script personalizado en lugar del default | `dhclient -sf /usr/local/sbin/dhclient-script` |
| `-w`              | Espera a que interfaz esté disponible         | `dhclient -w`                                  |
| `--timeout [seg]` | Configura timeout de espera                   | `dhclient --timeout 30 eth0`                   |
| `-4`              | Usa solo DHCPv4 (por defecto)                 | `dhclient -4 eth0`                             |
| `-6`              | Usa DHCPv6                                    | `dhclient -6 eth0`                             |
| `-1`              | Intenta obtener concesión solo una vez        | `dhclient -1 eth0`                             |