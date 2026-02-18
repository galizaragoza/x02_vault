| **Parámetro**     | **Función**                                                    | **Sintaxis de ejemplo**                                         |
| ------------- | ---------------------------------------------------------- | ----------------------------------------------------------- |
| `-f <config>` | Especifica archivo de configuración alternativo            | `proxychains -f /ruta/alternativa/proxychains.conf comando` |
| `-q`          | Modo silencioso (no muestra información de conexión)       | `proxychains -q firefox`                                    |
| `--help`      | Muestra ayuda y opciones disponibles                       | `proxychains --help`                                        |
| `--version`   | Muestra la versión de proxychains                          | `proxychains --version`                                     |
| Sin parámetro | Ejecuta comando con proxy usando configuración por defecto | `proxychains curl ifconfig.me`                              |