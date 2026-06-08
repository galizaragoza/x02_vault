Programa que proporciona vista dinámica en tiempo real de un sistema en ejecución. Muestra resumen del sistema (uptime, cargas, estados de tareas/CPU, uso de memoria) y lista de procesos/hilos con campos configurables. Soporta manipulación interactiva, configuración persistente y modos de visualización alternos.

| **Parámetro**                        | **Función**                                                                            | **Ejemplo**          |
| -------------------------------- | ---------------------------------------------------------------------------------- | ---------------- |
| `-b, --batch`                    | Ejecuta en modo batch para salida a programas/archivos, sin entrada.               | `top -b -n 10`   |
| `-c, --cmdline-toggle`           | Inicia con estado 'c' invertido (muestra líneas de comando o nombres de programa). | `top -c`         |
| `-d, --delay=SECS`               | Especifica retraso entre actualizaciones de pantalla.                              | `top -d 1.5`     |
| `-E, --scale-summary-mem=unidad` | Fuerza escala de memoria en resumen (k/m/g/t/p/e).                                 | `top -E m`       |
| `-e, --scale-task-mem=unidad`    | Fuerza escala de memoria en tareas (k/m/g/t/p).                                    | `top -e g`       |
| `-H, --threads-show`             | Muestra hilos individuales en lugar de suma por proceso.                           | `top -H`         |
| `-h, --help`                     | Muestra texto de ayuda y sale.                                                     | `top -h`         |
| `-i, --idle-toggle`              | Inicia con estado 'i' invertido (oculta tareas inactivas).                         | `top -i`         |
| `-n, --iterations=NUMBER`        | Máximo número de iteraciones antes de terminar.                                    | `top -n 5`       |
| `-o, --sort-override=FIELDNAME`  | Ordena tareas por campo especificado (+ alto a bajo, - bajo a alto).               | `top -o +%CPU`   |
| `-p, --pid=PIDLIST`              | Monitorea solo procesos con IDs especificados (hasta 20).                          | `top -p 123,456` |
| `-S, --accum-time-toggle`        | Inicia con modo tiempo acumulativo (incluye hijos muertos).                        | `top -S`         |
| `-s, --secure-mode`              | Fuerza modo seguro, incluso para root.                                             | `top -s`         |
| `-U, --filter-any-user=USER`     | Muestra solo procesos de usuario coincidente (cualquier UID).                      | `top -U root`    |
| `-u, --filter-only-euser=USER`   | Muestra solo procesos de usuario efectivo coincidente.                             | `top -u root`    |
| `-V, --version`                  | Muestra versión y sale.                                                            | `top -V`         |
| `-w, --width=COLUMNS`            | Establece ancho de salida (hasta 512 en batch).                                    | `top -w 80`      |
| `-1, --single-cpu-toggle`        | Inicia con estados CPU en una línea o separados.                                   | `top -1`         |