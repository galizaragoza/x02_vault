Monta filesystems en un directorio para acceder a ellos. Puede ser utilizado para acceder a particiones, imágenes de disco forenses o USB sticks.
```
mount [opciones] dispositivo directorio
```

```
mount /dev/sda1 /mnt
```

| **Parámetro** | **Función**                                                                          | **Ejemplo de Sintaxis**            |
| ------------- | ------------------------------------------------------------------------------------ | ---------------------------------- |
| `-t` tipo     | Especifica tipo de sistema de archivos (e.g., ext4, ntfs).                           | `mount -t ext4 /dev/sdb1 /mnt`     |
| `-o` opciones | Añade opciones como rw (lectura/escritura), ro (solo lectura), loop (para imágenes). | `mount -o loop,ro imagen.iso /mnt` |
| `-r`          | Monta en modo solo lectura (equivalente a -o ro).                                    | `mount -r /dev/sdc1 /mnt`          |
| `-v`          | Modo verbose, muestra detalles de operación.                                         | `mount -v /dev/sdd1 /mnt`          |
| `-L` etiqueta | Monta por etiqueta de volumen.                                                       | `mount -L MIVOLUMEN /mnt`          |
| `-U` uuid     | Monta por UUID del dispositivo.                                                      | `mount -U 1234-5678 /mnt`          |