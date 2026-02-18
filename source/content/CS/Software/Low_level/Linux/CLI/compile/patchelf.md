| **Parámetro**             | **Función**                                                                  | **Ejemplo de Sintaxis**                                                     |
| --------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------------------------- |
| `--set-interpreter`   | Cambia el cargador dinámico (interpreter) del ejecutable                 | `patchelf --set-interpreter /lib64/ld-linux-x86-64.so.2 mi_programa`    |
| `--set-rpath`         | Establece RPATH (lista de directorios para buscar bibliotecas)           | `patchelf --set-rpath '/usr/local/lib:$ORIGIN/lib' mi_app`              |
| `--set-runpath`       | Establece RUNPATH (similar a RPATH pero con diferente orden de búsqueda) | `patchelf --set-runpath '/opt/libs:$ORIGIN' mi_app`                     |
| `--print-interpreter` | Muestra el interpreter actual del ejecutable                             | `patchelf --print-interpreter /bin/ls`                                  |
| `--print-rpath`       | Muestra el RPATH actual del ejecutable                                   | `patchelf --print-rpath /usr/bin/mi_programa`                           |
| `--print-runpath`     | Muestra el RUNPATH actual del ejecutable                                 | `patchelf --print-runpath /usr/bin/mi_programa`                         |
| `--remove-rpath`      | Elimina el RPATH del ejecutable                                          | `patchelf --remove-rpath ejecutable`                                    |
| `--remove-needed`     | Elimina una entrada DT_NEEDED (dependencia de biblioteca)                | `patchelf --remove-needed libobsoleta.so mi_app`                        |
| `--add-needed`        | Añade una entrada DT_NEEDED (dependencia de biblioteca)                  | `patchelf --add-needed libnueva.so mi_app`                              |
| `--replace-needed`    | Reemplaza una dependencia de biblioteca por otra                         | `patchelf --replace-needed libvieja.so libnueva.so app`                 |
| `--force-rpath`       | Fuerza el uso de RPATH en lugar de RUNPATH                               | `patchelf --force-rpath --set-rpath '/usr/lib' app`                     |
| `--shrink-rpath`      | Elimina directorios innecesarios del RPATH                               | `patchelf --shrink-rpath ejecutable`                                    |
| `--output`            | Especifica archivo de salida (por defecto modifica el original)          | `patchelf --set-interpreter /lib/ld-linux.so.2 --output salida entrada` |