Muestra uso de ancho de banda en una interfaz por pares de hosts.

```
iftop [opciones]
```

| **Parámetro** | **Función**                                           | **Ejemplo**                   |
| --------- | ------------------------------------------------- | ------------------------- |
| `-i`      | Especifica interfaz de red.                       | `iftop -i eth0`           |
| `-f`      | Aplica filtro BPF a paquetes.                     | `iftop -f "port 80"`      |
| `-n`      | Suprime lookups de hostnames.                     | `iftop -n`                |
| `-N`      | Muestra puertos en lugar de nombres de servicios. | `iftop -N`                |
| `-F`      | Analiza tráfico en/out de red IPv4 específica.    | `iftop -F 192.168.0.0/24` |
