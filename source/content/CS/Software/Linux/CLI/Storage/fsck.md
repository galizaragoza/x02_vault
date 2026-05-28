Verificar y reparar sistemas de archivos Linux.

```
fsck [opciones] [dispositivo]
```

| **Parámetro** | **Función**                                    | **Ejemplo**                  |
| --------- | ------------------------------------------ | ------------------------ |
| `-A`      | Verifica todos los sistemas en /etc/fstab. | `fsck -A`                |
| `-s`      | Ejecuta verificaciones en serie.           | `fsck -s /dev/sdb1`      |
| `-t`      | Especifica tipo de sistema de archivos.    | `fsck -t ext2 /dev/sdb1` |
| `-M`      | Omite sistemas montados.                   | `fsck -M -A`             |
| `-N`      | Simula ejecución sin cambios.              | `fsck -N /dev/sdb1`      |
| `-y`      | Repara automáticamente.                    | `fsck -y /dev/sdb1`      |
