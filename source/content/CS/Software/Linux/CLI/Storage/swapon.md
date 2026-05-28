Activar swap en un dispositivo o archivo.

```
swapon [opciones] [dispositivo]
```

| **Parámetro** | **Función**                   | **Ejemplo**              |
| ------------- | ----------------------------- | ------------------------ |
| `-a`          | Activa todos en /etc/fstab.   | `swapon -a`              |
| `-p`          | Establece prioridad.          | `swapon -p 10 /dev/sdb1` |
| `-s`          | Muestra estado de swap.       | `swapon -s`              |
| `-d`          | Descarta páginas defectuosas. | `swapon -d /dev/sdb1`    |
| `-v`          | Modo verbose.                 | `swapon -v /dev/sdb1`    |
|               |                               |                          |
|               |                               |                          |
| **Parámetro** | **Función**                   | **Ejemplo**              |
| `-a`          | Desactiva todos.              | `swapoff -a`             |
