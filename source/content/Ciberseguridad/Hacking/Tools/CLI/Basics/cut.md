Extrae secciones específicas de líneas de texto
```bash
cut [opciones] [archivo]
```

| Parámetro                | Función                                          | Ejemplo de Sintaxis                                |
| ------------------------ | ------------------------------------------------ | -------------------------------------------------- |
| `-f` lista               | Selecciona campos (delimitados (por defecto tab) | `cut -f1,3 /etc/passwd`                            |
| `-d` 'delim'             | Define delimitador personalizado                 | `cut -d: -f1 /etc/passwd`                          |
| `-c` lista               | Selecciona por posición de caracteres            | `cut -c1-10 archivo.txt`                           |
| `-b` lista               | Selecciona por posición de bytes                 | `cut -b1-50 archivo.bin`                           |
| `--complement`           | Invierte la selección                            | `cut -d: -f1 --complement /etc/passwd`             |
| `--output-delimiter`='X' | Cambia delimitador de salida                     | `cut -d: -f1,7 --output-delimiter=' ' /etc/passwd` |
