Lee de entrada estándar y escribe en salida estándar y uno o más archivos simultáneamente.

```
tee [OPTION]... [FILE]...
```

| **Parámetro (Flag)**          | **Función**                                                              | **Ejemplo de Sintaxis** |
| ------------------------- | -------------------------------------------------------------------- | ------------------- |
| `-a, --append`            | Agrega en lugar de sobrescribir archivos.                            | ``echo "texto"      |
| `-i, --ignore-interrupts` | Ignora señales de interrupción.                                      | ``comando           |
| `-p`                      | Opera en modo para pipes, diagnostica errores.                       | ``comando           |
| `--output-error[=MODE]`   | Configura comportamiento en errores de escritura (warn, exit, etc.). | ``comando           |
| `--help`                  | Muestra ayuda.                                                       | `tee --help`        |
| `--version`               | Muestra versión.                                                     | `tee --version`     |