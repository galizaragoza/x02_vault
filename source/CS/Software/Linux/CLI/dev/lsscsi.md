Listar dispositivos SCSI/NVMe.

```
lsscsi [opciones]
```

| **Parámetro**   | **Función**                       | **Ejemplo de Sintaxis** |
| ----------- | ----------------------------- | ------------------- |
| `-g`        | Muestra dispositivo genérico. | `lsscsi -g`         |
| `-H`        | Lista hosts.                  | `lsscsi -H`         |
| `-l`        | Info detallada.               | `lsscsi -lll`       |
| `-k`        | Nombres kernel.               | `lsscsi -k`         |
| `-i`        | Muestra SCSI ID.              | `lsscsi -i`         |
| `-t`        | Info transporte.              | `lsscsi -t`         |
| `[H:C:T:L]` | Filtra por tupla.             | `lsscsi [1:0:0]`    |