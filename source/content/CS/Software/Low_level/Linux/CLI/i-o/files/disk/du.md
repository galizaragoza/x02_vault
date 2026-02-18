```
du [OPCIONES] [ARCHIVO|DIRECTORIO]
```

| **Parámetro**                 | **Función**                                                   | **Ejemplo de Sintaxis**                 |
| ------------------------- | --------------------------------------------------------- | ----------------------------------- |
| `-h`, `--human-readable`  | Muestra tamaños en formato legible (K, M, G)              | `du -h archivo.txt`                 |
| `-s`, `--summarize`       | Solo muestra el total para cada argumento                 | `du -sh /var/log`                   |
| `-c`, `--total`           | Muestra un total general al final                         | `du -ch *.log`                      |
| `-a`, `--all`             | Muestra tamaño de todos los archivos, no solo directorios | `du -ah /tmp`                       |
| `--max-depth=N`           | Limita la profundidad de directorios a mostrar            | `du -h --max-depth=1 /`             |
| `-t`, `--threshold=SIZE`  | Solo muestra entradas con tamaño >= SIZE                  | `du -ah -t 100M /`                  |
| `-x`, `--one-file-system` | No cruza límites de sistemas de archivos                  | `du -xh /`                          |
| `-L`, `--dereference`     | Sigue enlaces simbólicos (calcula el destino)             | `du -Lh /usr/bin`                   |
| `-S`, `--separate-dirs`   | No incluye tamaño de subdirectorios en directorios padres | `du -Sh /var`                       |
| `--exclude=PATTERN`       | Excluye archivos que coincidan con el patrón              | `du -h --exclude="*.log" /var`      |
| `--time`                  | Muestra fecha de última modificación                      | `du -h --time /var/log`             |
| `--apparent-size`         | Muestra tamaños aparentes, no uso en disco real           | `du -h --apparent-size archivo.txt` |
| `-b`, `--bytes`           | Muestra tamaño en bytes                                   | `du -b archivo.txt`                 |