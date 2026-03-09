| **Parámetro/Opción** | **Función**                                 | **Ejemplo de Sintaxis**        |
| ---------------- | --------------------------------------- | -------------------------- |
| `-T`             | Ejecuta en modo texto                   | `ettercap -T`              |
| `-G`             | Ejecuta con interfaz gráfica GTK        | `ettercap -G`              |
| `-C`             | Modo interfaz de caracteres             | `ettercap -C`              |
| `-M <método>`    | Inicia ataque MITM especificando método | `ettercap -M arp`          |
| `-i interfaz`    | Especifica interfaz de red              | `ettercap -i eth0`         |
| `-q`             | Modo silencioso (solo contraseñas)      | `ettercap -Tq`             |
| `-w archivo`     | Escribe captura a archivo pcap          | `ettercap -w captura.pcap` |
| `-r archivo`     | Lee desde archivo pcap                  | `ettercap -r archivo.pcap` |
| `-L archivo`     | Log detallado en archivo                | `ettercap -L log.txt`      |
| `-s tamaño`      | Define tamaño de snapshots              | `ettercap -s 2048`         |
| `-P plugin`      | Carga plugin específico                 | `ettercap -P dns_spoof`    |
| `-S`             | Activa seguimiento de conexiones SSL    | `ettercap -S`              |
| `-z`             | Activa modo pasivo (solo sniffing)      | `ettercap -z`              |