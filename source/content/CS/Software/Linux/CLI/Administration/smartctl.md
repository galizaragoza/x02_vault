Parte del paquete `smartmontools`, `smartctl` se utiliza para controlar y monitorear el sistema S.M.A.R.T. (Self-Monitoring, Analysis and Reporting Technology) integrado en la mayoría de los discos duros (HDD), SSDs y unidades NVMe modernos.

| **Parámetro** | **Función**                                                                        | **Ejemplo de Sintaxis**      |
| ------------- | ---------------------------------------------------------------------------------- | ---------------------------- |
| `-i`          | Muestra información básica del dispositivo (modelo, número de serie, firmware).    | `smartctl -i /dev/sda`       |
| `-H`          | Muestra el estado de salud general (PASSED/FAILED).                                | `smartctl -H /dev/nvme0`     |
| `-a`          | Muestra toda la información SMART disponible (atributos, logs y errores).          | `smartctl -a /dev/sda`       |
| `-t [tipo]`   | Ejecuta una prueba de autodiagnóstico (short, long, conveyance).                   | `smartctl -t short /dev/sda` |
| `-l error`    | Muestra el registro de errores de SMART.                                           | `smartctl -l error /dev/sda` |
| `-c`          | Indica qué capacidades y pruebas soporta el disco.                                 | `smartctl -c /dev/sda`       |
| `-x`          | Muestra información extendida, incluyendo estadísticas de temperatura y vida útil. | `smartctl -x /dev/sda`       |
