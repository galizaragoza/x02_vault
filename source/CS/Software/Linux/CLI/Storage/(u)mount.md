Monta filesystems en un directorio para acceder a ellos. Puede ser utilizado para acceder a particiones, imágenes de disco forenses o USB sticks.

```
mount [opciones] dispositivo directorio
```

```
mount /dev/sda1 /mnt
```

```sh
mount
## Output template

/dev/sda1 on / type ext4 (rw,errors=remount-ro)
## <device> on <mount point> type <filesystem type> <mount options> 
```

| **Parámetro** | **Función**                                                                          | **Ejemplo de Sintaxis**            |
| ------------- | ------------------------------------------------------------------------------------ | ---------------------------------- |
| `-t` tipo     | Especifica tipo de sistema de archivos (e.g., ext4, ntfs).                           | `mount -t ext4 /dev/sdb1 /mnt`     |
| `-o` opciones | Añade opciones como rw (lectura/escritura), ro (solo lectura), loop (para imágenes). | `mount -o loop,ro imagen.iso /mnt` |
| `-r`          | Monta en modo solo lectura (equivalente a -o ro).                                    | `mount -r /dev/sdc1 /mnt`          |
| `-v`          | Modo verbose, muestra detalles de operación.                                         | `mount -v /dev/sdd1 /mnt`          |
| `-L` etiqueta | Monta por etiqueta de volumen.                                                       | `mount -L MIVOLUMEN /mnt`          |
| `-U` uuid     | Monta por UUID del dispositivo.                                                      | `mount -U 1234-5678 /mnt`          |


## umount
```
umount <mountpoint> 
```

| **Parámetro (Flag)** | **Función**                                      | **Ejemplo de Sintaxis**   |
| ---------------- | -------------------------------------------- | --------------------- |
| `-f`             | Fuerza desmontaje (útil si ocupado).         | `umount -f /mnt`      |
| `-l`             | Desmontaje lazy (desacopla inmediatamente).  | `umount -l /dev/sdb1` |
| `-r`             | Intenta remontar read-only si falla.         | `umount -r /mnt`      |
| `-a`             | Desmonta todos los filesystems en /etc/mtab. | `umount -a`           |
| `-t`             | Desmonta por tipo de filesystem.             | `umount -t ext4`      |
| `-v`             | Modo verbose.                                | `umount -v /mnt`      |
