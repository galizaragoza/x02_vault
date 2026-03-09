| **Parámetro** | **Función**                                 | **Ejemplo de sintaxis**    |
| --------- | --------------------------------------- | ---------------------- |
| `if=`     | Especifica el archivo/entrada de origen | `dd if=/dev/sda`       |
| `of=`     | Especifica el archivo/salida de destino | `dd of=backup.img`     |
| `bs=`     | Define el tamaño del bloque en bytes    | `dd bs=4M`             |
| `count=`  | Número de bloques a copiar              | `dd count=100`         |
| `skip=`   | Omite bloques al inicio de la entrada   | `dd skip=10`           |
| `seek=`   | Omite bloques al inicio de la salida    | `dd seek=10`           |
| `conv=`   | Aplica conversiones (ej: noerror,sync)  | `dd conv=noerror,sync` |
| `status=` | Controla la información mostrada        | `dd status=progress`   |

# Ejemplos
```sh
# Crear imagen de un disco completo
dd if=/dev/sda of=/backup/disk.img bs=4M status=progress

# Escribir ISO a USB
dd if=ubuntu.iso of=/dev/sdb bs=4M status=progress conv=fdatasync

# Generar archivo de 1GB con ceros
dd if=/dev/zero of=testfile.bin bs=1M count=1000

# Clonar disco a disco
dd if=/dev/sda of=/dev/sdb bs=4M status=progress
```