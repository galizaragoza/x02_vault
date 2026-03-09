```
df [OPCIONES] [ARCHIVO|DIRECTORIO|DISPOSITIVO]
```

```sh
df ## Output

Filesystem 1K-blocks Used Available Use% Mounted on
/dev/sda1 214234312 127989560 75339204 63% /

##1K-blocks: The total capacity of the filesystem in blocks of 1,024 bytes
##Used: The number of occupied blocks
##Available: The number of free blocks
##Use%: The percentage of blocks in use 
```


| **Parámetro**                            | **Función**                                             | **Ejemplo de Sintaxis**                                      |
| ------------------------------------ | --------------------------------------------------- | -------------------------------------------------------- |
| `-a`, `--all`                        | Incluye sistemas de archivos ficticios (como /proc) | `df -a`                                                  |
| `-h`, `--human-readable`             | Muestra tamaños en formato legible (K, M, G)        | `df -h`                                                  |
| `-H`, `--si`                         | Usa potencias de 1000 en lugar de 1024              | `df -H`                                                  |
| `-i`, `--inodes`                     | Muestra información de inodos en lugar de bloques   | `df -i`                                                  |
| `-k`, `--block-size=1K`              | Usa bloques de 1KB para el cálculo                  | `df -k`                                                  |
| `-l`, `--local`                      | Solo sistemas de archivos locales                   | `df -l`                                                  |
| `-P`, `--portability`                | Formato de salida POSIX (evita líneas truncadas)    | `df -P`                                                  |
| `-t <tipo>`, `--type=<tipo>`         | Filtra por tipo de sistema de archivos              | `df -t ext4`                                             |
| `-x <tipo>`, `--exclude-type=<tipo>` | Excluye tipos de sistemas de archivos               | `df -x tmpfs`                                            |
| `-T`, `--print-type`                 | Muestra el tipo de sistema de archivos              | `df -T`                                                  |
| `--total`                            | Muestra una línea de resumen total                  | `df --total`                                             |
| `--output=<campos>`                  | Campos específicos a mostrar                        | `df --output=source,fstype,size,used,avail,pcent,target` |

