```sh
15 09 * * * /home/juser/bin/spmake ## Un wildcard significa todos los valores, es decir, el cron de arriba se ejecuta todos los días a las 09:15.

15 09 5,14 * * /home/juser/bin/spmake ## Este, los días 5 y 14 de cada mes a las 09:15
```

| **Unidad**       | **Representación**     | ///// |     |           | **Shortcuts** |          |
| ---------------- | ---------------------- | ----- | --- | --------- | ------------- | -------- |
| Minuto           | 0-59                   | ///// |     | @midnight | @monthly      | @yearly  |
| Hora             | 0-23                   | ///// |     | @noon     | @weekly       | @anually |
| Día del mes      | 1-31                   | ///// |     | @reboot   | @daily        |          |
| Mes              | 1-12                   | ///// |     |           |               |          |
| Día de la semana | 0-7 (0 y 7 es domingo) | ///// |     |           |               |          |



![[cron.png]]


# Parámetros `crontab`

| **Parámetro** | **Función**                                                                | **Ejemplo**                     |
| --------- | ---------------------------------------------------------------------- | --------------------------- |
| `-e`      | Edita el crontab actual (usa editor de $VISUAL o $EDITOR).             | `crontab -e`                |
| `-l`      | Lista el contenido del crontab actual.                                 | `crontab -l`                |
| `-r`      | Elimina el crontab actual.                                             | `crontab -r`                |
| `-u user` | Especifica el usuario cuyo crontab se gestiona (requiere privilegios). | `crontab -u usuario -l`     |
| `-i`      | Pregunta antes de eliminar (con -r).                                   | `crontab -r -i`             |
| `file`    | Instala nuevo crontab desde archivo (sin flag, reemplaza existente).   | `crontab nuevo_crontab.txt` |
| `-V`      | Muestra versión y sale.                                                | `crontab -V`                |
| `-h`      | Muestra ayuda y sale.                                                  | `crontab -h`                |