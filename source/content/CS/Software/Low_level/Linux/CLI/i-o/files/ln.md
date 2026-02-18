
| **Parámetro**                      | **Función**                           | **Ejemplo de sintaxis**                 |
| ------------------------------ | --------------------------------- | ----------------------------------- |
| `-s` o `--symbolic`            | Crea enlace simbólico             | `ln -s /ruta/origen /ruta/enlace`   |
| `-f` o `--force`               | Fuerza creación (sobreescribe)    | `ln -sf archivo.txt enlace.txt`     |
| `-n` o `--no-dereference`      | Trata destino como archivo normal | `ln -sn archivo dir/enlace`         |
| `-i` o `--interactive`         | Pregunta antes de sobreescribir   | `ln -i archivo enlace`              |
| `-b` o `--backup`              | Crea backup de destino existente  | `ln -b archivo enlace`              |
| `-S` o `--suffix`              | Especifica sufijo para backups    | `ln -S .backup archivo enlace`      |
| `-t` o `--target-directory`    | Especifica directorio destino     | `ln -t /destino/ archivo1 archivo2` |
| `-T` o `--no-target-directory` | Trata destino como archivo normal | `ln -T archivo destino`             |
| `-L` o `--logical`             | Desreferencia enlaces simbólicos  | `ln -L enlace nuevo_enlace`         |
| `-P` o `--physical`            | Copia enlaces físicamente         | `ln -P enlace nuevo_enlace`         |
| `-r` o `--relative`            | Crea enlaces con rutas relativas  | `ln -rs ../archivo enlace`          |
| `-v` o `--verbose`             | Modo verboso (muestra acciones)   | `ln -v archivo enlace`              |
| `--help`                       | Muestra ayuda de uso              | `ln --help`                         |
| `--version`                    | Muestra versión                   | `ln --version`                      |