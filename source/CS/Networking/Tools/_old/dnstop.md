Recopila y muestra estadísticas de tráfico DNS en la red.

```
dnstop [opciones] [dispositivo] [savefile]
```

| **Parámetro** | **Función**                           | **Ejemplo**                      |
| --------- | --------------------------------- | ---------------------------- |
| `-4`      | Cuenta solo mensajes con IPv4.    | `dnstop -4 eth0`             |
| `-6`      | Cuenta solo mensajes con IPv6.    | `dnstop -6 eth0`             |
| `-a`      | Anonimiza direcciones.            | `dnstop -a eth0`             |
| `-b`      | Aplica filtro BPF.                | `dnstop -b "port 53" eth0`   |
| `-i`      | Ignora direcciones seleccionadas. | `dnstop -i 192.168.1.1 eth0` |
| `-p`      | No usa modo promiscuo.            | `dnstop -p eth0`             |
