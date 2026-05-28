Traduce, elimina o comprime caracteres en la entrada estándar (stdin). Ideal en hacking para limpiar logs, extraer flags, normalizar texto, eliminar saltos de línea o caracteres no deseados.

```
tr [OPCIONES] CONJUNTO1 [CONJUNTO2]
```

Lee de stdin y escribe a stdout

| **Flag**            | **Función**                                              | **Ejemplo de sintaxis**    |
| ------------------- | -------------------------------------------------------- | -------------------------- |
| `-d`                | Elimina caracteres de CONJUNTO1                          | `tr -d '\n'`               |
| `-s`                | Comprime secuencias repetidas de CONJUNTO1               | `tr -s ' '`                |
| `-c`                | Usa complemento de CONJUNTO1 (todo lo que no está en él) | `tr -cd '[:alnum:]'`       |
| `-C`                | Sustituye newline por espacio                            | `tr -C '\n' ' '`           |
| `--delete`          | Igual que `-d`                                           | `tr --delete '[:space:]'`  |
| `--squeeze-repeats` | Igual que `-s`                                           | `tr --squeeze-repeats ' '` |
