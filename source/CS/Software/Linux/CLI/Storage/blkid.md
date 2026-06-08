```
blkid [OPCIONES] [DISPOSITIVO]
```

| **Parámetro**       | **Función**                                             | **Ejemplo de Sintaxis**         |
| --------------- | --------------------------------------------------- | --------------------------- |
| `-c <archivo>`  | Especifica archivo de caché alternativo             | `blkid -c /tmp/blkid.tmp`   |
| `-g`            | Realiza recolección de basura en caché              | `blkid -g`                  |
| `-o <formato>`  | Formato de salida (lista, valor, dispositivo, etc.) | `blkid -o list`             |
| `-p`            | Modo de sondeo directo (evita caché)                | `blkid -p /dev/sda1`        |
| `-s <tag>`      | Muestra solo etiquetas específicas                  | `blkid -s UUID`             |
| `-i`            | Muestra información adicional/I/O limits            | `blkid -i /dev/sda`         |
| `-L <etiqueta>` | Busca dispositivo por etiqueta                      | `blkid -L ROOT`             |
| `-U <uuid>`     | Busca dispositivo por UUID                          | `blkid -U 123e4567-e89b...` |
| `-h`            | Muestra ayuda                                       | `blkid -h`                  |
| `-V`            | Muestra versión                                     | `blkid -V`                  |