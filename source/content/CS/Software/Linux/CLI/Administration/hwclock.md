Herramienta de administración para el reloj de hardware (RTC). Permite mostrar la hora del reloj de hardware, establecerla a un tiempo específico, sincronizar con el reloj del sistema, compensar derivas, corregir la escala de tiempo del sistema y predecir valores futuros basados en la deriva.

| **Parámetro**                  | **Función**                                                                       | **Ejemplo**                                      |
| ------------------------------ | --------------------------------------------------------------------------------- | ------------------------------------------------ |
| `-r, --show`                   | Lee y muestra la hora del reloj de hardware en formato ISO 8601.                  | `hwclock --show`                                 |
| `-s, --hctosys`                | Establece el reloj del sistema desde el reloj de hardware, compensando deriva.    | `hwclock --hctosys`                              |
| `-w, --systohc`                | Establece el reloj de hardware desde el reloj del sistema y actualiza timestamps. | `hwclock --systohc`                              |
| `--set --date=date_string`     | Establece el reloj de hardware a la fecha especificada (en tiempo local).         | `hwclock --set --date='2025-12-24 12:00:00'`     |
| `-a, --adjust`                 | Ajusta el reloj de hardware por deriva sistemática.                               | `hwclock --adjust`                               |
| `-u, --utc`                    | Indica que el reloj de hardware usa UTC.                                          | `hwclock --utc --show`                           |
| `-l, --localtime`              | Indica que el reloj de hardware usa tiempo local.                                 | `hwclock --localtime --show`                     |
| `--predict --date=date_string` | Predice la hora futura del reloj de hardware basada en deriva.                    | `hwclock --predict --date='2026-01-01 00:00:00'` |
| `--update-drift`               | Actualiza el factor de deriva en /etc/adjtime.                                    | `hwclock --update-drift --systohc`               |
| `-f, --rtc=filename`           | Especifica el archivo de dispositivo RTC alternativo.                             | `hwclock --rtc=/dev/rtc0 --show`                 |
| `-D, --debug`                  | Muestra detalles internos (deprecado, usa --verbose).                             | `hwclock --debug --show`                         |
| `-v, --verbose`                | Muestra más detalles sobre operaciones.                                           | `hwclock --verbose --show`                       |
| `-h, --help`                   | Muestra ayuda y sale.                                                             | `hwclock --help`                                 |
| `-V, --version`                | Muestra versión y sale.                                                           | `hwclock --version`                              |