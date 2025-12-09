Ejecuta un comando con prioridad de programación modificada (niceness) para ajustar el uso de CPU, permitiendo procesos de bajo impacto.
```
nice [opciones] comando [argumentos]
```

| Parámetro       | Función                                                                          | Ejemplo de Sintaxis         |
| --------------- | -------------------------------------------------------------------------------- | --------------------------- |
| `-n nivel`      | Establece el nivel de niceness (-20 alta prioridad, 19 baja; predeterminado 10). | `nice -n 15 stress --cpu 4` |
| `-h, --help`    | Muestra ayuda y sintaxis.                                                        | `nice --help`               |
| `-V, --version` | Muestra versión del comando.                                                     | `nice --version`            |