`modprobe` es una herramienta inteligente para cargar y descargar módulos del kernel. Resuelve dependencias automáticamente y consulta `/lib/modules/$(uname -r)/modules.dep` para las relaciones entre módulos.

| **Parámetro**               | **Función**                                  | **Ejemplo de sintaxis**              |
| ----------------------- | ---------------------------------------- | -------------------------------- |
| `-r` o `--remove`       | Descarga módulo y sus dependencias       | `modprobe -r mi_modulo`          |
| `-a` o `--all`          | Carga múltiples módulos                  | `modprobe -a modulo1 modulo2`    |
| `-v` o `--verbose`      | Muestra detalles de la operación         | `modprobe -v mi_modulo`          |
| `-n` o `--dry-run`      | Simula sin realizar cambios reales       | `modprobe -n mi_modulo`          |
| `-q` o `--quiet`        | Suprime mensajes de error                | `modprobe -q mi_modulo`          |
| `-c` o `--showconfig`   | Muestra configuración actual             | `modprobe -c`                    |
| `-l` o `--list`         | Lista todos los módulos disponibles      | `modprobe -l`                    |
| `-C` o `--config=`      | Usa archivo de configuración alternativo | `modprobe -C /etc/modprobe.conf` |
| `-D` o `--show-depends` | Muestra dependencias del módulo          | `modprobe -D mi_modulo`          |