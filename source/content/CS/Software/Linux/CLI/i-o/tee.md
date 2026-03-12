Lee de entrada estándar y escribe en salida estándar y uno o más archivos simultáneamente.

```
tee [OPTION]... [FILE]...
```

| **Parámetro (Flag)**      | **Función**                                                          |
| ------------------------- | -------------------------------------------------------------------- |
| `-a, --append`            | Agrega en lugar de sobrescribir archivos.                            |
| `-i, --ignore-interrupts` | Ignora señales de interrupción.                                      |
| `-p`                      | Opera en modo para pipes, diagnostica errores.                       |
| `--output-error[=MODE]`   | Configura comportamiento en errores de escritura (warn, exit, etc.). |
| `--help`                  | Muestra ayuda.                                                       |
| `--version`               | Muestra versión.                                                     |
