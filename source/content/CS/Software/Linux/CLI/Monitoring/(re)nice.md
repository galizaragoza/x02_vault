Ejecuta un comando con prioridad de programación modificada (niceness) para ajustar el uso de CPU, permitiendo procesos de bajo impacto.
```
nice [opciones] comando [argumentos]
```

# nice

| Parámetro       | Función                                                                          | Ejemplo de Sintaxis         |
| --------------- | -------------------------------------------------------------------------------- | --------------------------- |
| `-n nivel`      | Establece el nivel de niceness (-20 alta prioridad, 19 baja; predeterminado 10). | `nice -n 15 stress --cpu 4` |
| `-h, --help`    | Muestra ayuda y sintaxis.                                                        | `nice --help`               |
| `-V, --version` | Muestra versión del comando.                                                     | `nice --version`            |

# renice
| Parameter             | Function                   | Example                         |
| --------------------- | -------------------------- | ------------------------------- |
| ``-n priority         | delta``                    | Set absolute/relative priority. |
| `--priority priority` | Set absolute priority.     | `renice --priority 5 987`       |
| `--relative delta`    | Set relative priority.     | `renice --relative +2 987`      |
| `-g, --pgrp`          | Use process group IDs.     | `renice +1 -g 123`              |
| `-p, --pid`           | Use process IDs (default). | `renice +1 -p 32`               |
| `-u, --user`          | Use usernames/IDs.         | `renice +1 -u daemon`           |