Crea filesystems en dispositivos o archivos, formateando almacenamiento para uso en Linux.
```
mkfs [options] [-t type] [fs-options] device [size]
```


# Params
| **Parámetro (Flag)** | **Función**                                    | **Ejemplo de Sintaxis**         |
| ---------------- | ------------------------------------------ | --------------------------- |
| `-t`             | Especifica tipo de filesystem.             | `mkfs -t ext4 /dev/sdb1`    |
| `-V`             | Modo verbose; muestra comandos ejecutados. | `mkfs -V -t ext4 /dev/sdb1` |
| `-h`             | Muestra ayuda.                             | `mkfs -h`                   |

## ext4
| **Parámetro (Flag)** | **Función**                            | **Ejemplo de Sintaxis**                     |
| ---------------- | -------------------------------------- | ------------------------------------------- |
| `-L`             | Establece etiqueta del volumen.        | `mkfs.ext4 -L mydata /dev/sdb1`             |
| `-m`             | Porcentaje de bloques reservados.      | `mkfs.ext4 -m 5 /dev/sdb1`                  |
| `-O`             | Habilita características específicas.  | `mkfs.ext4 -O extents,dir_index /dev/sdb1`  |
| `-E`             | Opciones extendidas (e.g., lazy init). | `mkfs.ext4 -E lazy_itable_init=1 /dev/sdb1` |

## vfat
| **Parámetro (Flag)** | **Función**                                    | **Ejemplo de Sintaxis**             |
| ---------------- | ------------------------------------------ | ------------------------------- |
| `-n`             | Establece etiqueta del volumen.            | `mkfs.vfat -n USBDRV /dev/sdb1` |
| `-I`             | Ignora advertencias de sector de arranque. | `mkfs.vfat -I /dev/sdb1`        |
| `-F`             | Especifica tamaño FAT (e.g., 32).          | `mkfs.vfat -F 32 /dev/sdb1`     |
| `-s`             | Sectores por cluster.                      | `mkfs.vfat -s 8 /dev/sdb1`      |