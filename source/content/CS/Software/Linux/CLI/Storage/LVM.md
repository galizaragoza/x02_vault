Gestiona volúmenes lógicos en Linux, permitiendo creación, redimensionamiento y manejo flexible de almacenamiento en discos.

```
vgs
```
# Params
## pvcreate
| Parámetro (Flag) | Función                               | Ejemplo de Sintaxis                     |
| ---------------- | ------------------------------------- | --------------------------------------- |
| `-f`             | Fuerza la operación sin confirmación. | `pvcreate -f /dev/sdb1`                 |
| `--uuid`         | Especifica UUID personalizado.        | `pvcreate --uuid abcdef-1234 /dev/sdb1` |
| `-y`             | Responde sí a todas las preguntas.    | `pvcreate -y /dev/sdb1`                 |


## pvdisplay
| Parámetro (Flag) | Función                      | Ejemplo de Sintaxis      |
| ---------------- | ---------------------------- | ------------------------ |
| `-v`             | Muestra información verbose. | `pvdisplay -v /dev/sdb1` |
| `-m`             | Muestra mapa de segmentos.   | `pvdisplay -m`           |
| `-C`             | Formato columnar.            | `pvdisplay -C`           |

## vgcreate

|Parámetro (Flag)|Función|Ejemplo de Sintaxis|
|---|---|---|
|`-s`|Especifica tamaño de extents.|`vgcreate -s 4M vg01 /dev/sdb1`|
|`-l`|Límite máximo de LVs.|`vgcreate -l 255 vg01 /dev/sdb1`|
|`-p`|Límite máximo de PVs.|`vgcreate -p 128 vg01 /dev/sdb1`|

## vgdisplay
|Parámetro (Flag)|Función|Ejemplo de Sintaxis|
|---|---|---|
|`-v`|Muestra información verbose.|`vgdisplay -v vg01`|
|`-s`|Muestra solo resumen.|`vgdisplay -s vg01`|
|`-C`|Formato columnar.|`vgdisplay -C`|

## lvcreate
| Parámetro (Flag) | Función                     | Ejemplo de Sintaxis                          |
| ---------------- | --------------------------- | -------------------------------------------- |
| `-L`             | Especifica tamaño en bytes. | `lvcreate -L 10G -n lv01 vg01`               |
| `-n`             | Nombre del LV.              | `lvcreate -L 10G -n lv01 vg01`               |
| `-s`             | Crea snapshot.              | `lvcreate -s -L 1G -n snap01 /dev/vg01/lv01` |

## lvdisplay
| Parámetro (Flag) | Función                      | Ejemplo de Sintaxis           |
| ---------------- | ---------------------------- | ----------------------------- |
| `-v`             | Muestra información verbose. | `lvdisplay -v /dev/vg01/lv01` |
| `-m`             | Muestra mapa de segmentos.   | `lvdisplay -m`                |
| `-C`             | Formato columnar.            | `lvdisplay -C`                |

## lvextend
| Parámetro (Flag) | Función                                  | Ejemplo de Sintaxis                 |
| ---------------- | ---------------------------------------- | ----------------------------------- |
| `-L`             | Añade tamaño específico.                 | `lvextend -L +5G /dev/vg01/lv01`    |
| `-r`             | Redimensiona filesystem automáticamente. | `lvextend -L +5G -r /dev/vg01/lv01` |
| `-l`             | Añade extents.                           | `lvextend -l +100 /dev/vg01/lv01`   |

## lvreduce
|Parámetro (Flag)|Función|Ejemplo de Sintaxis|
|---|---|---|
|`-L`|Reduce a tamaño específico.|`lvreduce -L 5G /dev/vg01/lv01`|
|`-r`|Redimensiona filesystem automáticamente.|`lvreduce -L 5G -r /dev/vg01/lv01`|
|`-f`|Fuerza sin confirmación.|`lvreduce -f -L 5G /dev/vg01/lv01`|

## lvremove
| Parámetro (Flag) | Función                                  | Ejemplo de Sintaxis                |
| ---------------- | ---------------------------------------- | ---------------------------------- |
| `-L`             | Reduce a tamaño específico.              | `lvreduce -L 5G /dev/vg01/lv01`    |
| `-r`             | Redimensiona filesystem automáticamente. | `lvreduce -L 5G -r /dev/vg01/lv01` |
| `-f`             | Fuerza sin confirmación.                 | `lvreduce -f -L 5G /dev/vg01/lv01` |