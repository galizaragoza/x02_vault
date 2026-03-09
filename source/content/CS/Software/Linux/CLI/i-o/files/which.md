
| **Parámetro**          | **Función**                                                                             | **Ejemplo de Sintaxis**                            |
| ------------------ | ----------------------------------------------------------------------------------- | ---------------------------------------------- |
| (sin parámetros)   | Muestra la ruta del primer comando encontrado en PATH                               | `which python`                                 |
| `-a`               | Muestra TODAS las rutas encontradas, no solo la primera                             | `which -a python`                              |
| `-s`               | Modo silencioso: solo devuelve código de salida (0=encontrado, 1=no encontrado)     | `which -s comando_inexistente`                 |
| `--skip-alias`     | Ignora aliases de shell (solo en algunas versiones)                                 | `which --skip-alias ls`                        |
| `--skip-functions` | Ignora funciones de shell (solo en algunas versiones)                               | `which --skip-functions prompt`                |
| `--read-alias`     | Lee aliases de stdin (solo en algunas versiones)                                    | `alias ls='ls --color'; which --read-alias ls` |
| `--read-functions` | Lee funciones de shell de stdin (solo en algunas versiones)                         | `which --read-functions nombre_funcion`        |
| `--tty-only`       | Desactiva el procesamiento si la entrada no es un terminal (solo algunas versiones) | `which --tty-only vim`                         |