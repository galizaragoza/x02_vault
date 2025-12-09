Ejecuta comandos con argumentos leídos `stdin`. Puede usarse para convertir las salidas de otros comandos en argumentos para otras herramientas.

```
comando | xargs [opciones] comando_a_ejecutar
```

| Parámetro                  | Función                                                                    | Ejemplo de sintaxis               |
| -------------------------- | -------------------------------------------------------------------------- | --------------------------------- |
| `-a` file                  | Lee argumentos de un archivo en vez de stdin                               | `cat hosts.txt                    |
| `-t`                       | Muestra el comando antes de ejecutarlo (debug)                             | `cat ips.txt                      |
| `-P` n                     | Ejecuta n procesos en paralelo (máxima velocidad)                          | `cat urls.txt                     |
| `-I` {}                    | Reemplaza {} por cada línea (permite usar el argumento en cualquier parte) | `cat ips.txt                      |
| `-n` 1                     | Usa máximo 1 argumento por comando (evita agrupar)                         | `cat domains.txt                  |
| `-d` '\n'                  | Usa salto de línea como delimitador (ignora espacios)                      | `printf "host1\nhost2\nhost3"     |
| `--max-args`=1             | Equivalente a -n 1                                                         | `cat files.txt                    |
| `-0`                       | Usa null como delimitador (útil con find -print0)                          | `find /path -name "*.php" -print0 |
| `-r` o `--no-run-if-empty` | No ejecuta si la entrada está vacía                                        | `cat maybe_empty.txt              |
