# ¿Qué es un File System?
Un filesystem o sistema de archivos consiste en una serie de normas y procesos mediante los cuáles se rige la gestión del almacenamiento en un dispositivo. Esto es vital para la organización, lectura, escritura y orden de los archivos contenidos en una unidad de almacenamiento, toma el rol de 'middleman' entre el OS y el almacenamiento conectado y según la versión, ayuda a mantener controles de acceso, seguridad, atributos y convenciones de nombres.
Básicamente, es un conjunto de instrucciones necesarias para poder acceder y modificar archivos, el filesystem sabe donde están, quién puede acceder a ellos, como se llaman...En resumen los organiza.
## Windows
### FAT32 (File Allocation Table)
Es el más compatible, ya que existe desde Windows95. Sin embargo, esta bastante anticuado y solo puede gestionar archivos de tamaños inferiores a 4GB, además, la partición máxima es de 2TB. En caso de querer añadir un disco de 4TB habría que particionarlo en dos.
### exFAT (extended File Allocation Table)
De 2006, se hizo como una mejora a FAT32, implementa mejoras en términos de capacidad de tamaño de archivos y particiones, aumentando estos límites a virtualmente ilimitados.
La parte negativa es que es menos compatible.
### NTFS (New Technology File System)
Es el filesystem por defecto de Windows, NTFS no tiene límite de tamaño de archivos o particiones, también tiene más características, como control de acceso, *shadow copies* o encriptado. También es menos compatible que FAT32

Este sistema tiene una vulnerabilidad llamada [[NTFS]]

![[windows-FS.png|871x379]]


|                                    | FAT32                               | exFAT                                                | NTFS                                          |
| ---------------------------------- | ----------------------------------- | ---------------------------------------------------- | --------------------------------------------- |
| **Compatibilidad**                 | Más compatible                      | Compatible con Windows y dispositivos y OSs modernos | Solo 100% compatible con Windows              |
| **Tamaño de partición y archivos** | 4GB por archivo y 2TB por partición | Sin límite                                           | Sin límite                                    |
| **Features**                       |                                     |                                                      | Seguridad, criptografía y *previous versions* |

## Fuentes / recursos
[Keepcoding blog](https://keepcoding.io/blog/que-es-un-sistema-de-archivos/)
[FAT32 vs exFAT vs NTFS - Windows File Systems](https://youtu.be/bYjQakUxeVY)