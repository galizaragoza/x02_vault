Utilidad que lee comandos de entrada estándar o archivo y los agrupa como un trabajo 'at-job' para ejecutarse en un tiempo futuro. Ejecuta en shell separado sin terminal controlador, reteniendo entorno, directorio y máscara de creación de archivos. Notifica al usuario vía mail si se especifica.

| **Parámetro**      | **Función**                                                                   | **Ejemplo**                        |
| -------------- | ------------------------------------------------------------------------- | ------------------------------ |
| `-f file`      | Especifica archivo con comandos en lugar de entrada estándar.             | `at -f script.sh now + 1 hour` |
| `-l`           | Lista trabajos programados para el usuario invocador.                     | `at -l`                        |
| `-m`           | Envía mail al usuario al completar el trabajo, incluyendo salida/errores. | `at -m 0730 tomorrow`          |
| `-q queuename` | Programa en cola específica (predeterminado 'a').                         | `at -q b now + 1 hour`         |
| `-r`           | Elimina trabajos especificados por at_job_id.                             | `at -r job1`                   |
| `-t time_arg`  | Programa en tiempo especificado (formato como touch -t).                  | `at -t 180000 script.sh`       |