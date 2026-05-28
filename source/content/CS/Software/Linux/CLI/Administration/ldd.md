ldd es una herramienta de línea de comandos en Linux que imprime las dependencias de bibliotecas compartidas (shared objects) requeridas por un programa o biblioteca compartida especificada. Invoca el enlazador dinámico para inspeccionar y mostrar dependencias, incluyendo indirectas, y puede realizar relocaciones para reportar objetos o funciones faltantes (solo ELF).

| **Flag**                    | **Descripción**                                                         | **Ejemplo**          |
| ----------------------- | ------------------------------------------------------------------- | ---------------- |
| `--version`             | Imprime el número de versión de ldd.                                | `ldd --version`  |
| `-v, --verbose`         | Imprime toda la información, incluyendo versiones de símbolos.      | `ldd -v /bin/ls` |
| `-u, --unused`          | Imprime dependencias directas no usadas.                            | `ldd -u /bin/ls` |
| `-d, --data-relocs`     | Realiza relocaciones de datos y reporta objetos faltantes (ELF).    | `ldd -d /bin/ls` |
| `-r, --function-relocs` | Realiza relocaciones de datos y funciones, reporta faltantes (ELF). | `ldd -r /bin/ls` |
| `--help`                | Muestra información de uso.                                         | `ldd --help`     |