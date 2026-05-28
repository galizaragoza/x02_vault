```
parted [opciones] dispositivo [comando [argumentos]]
```

| **Parámetro (Flag)** | **Función**                                         | **Ejemplo de Sintaxis**                             |
| ---------------- | ----------------------------------------------- | ----------------------------------------------- |
| `-s`             | Ejecuta comandos sin interacción (script mode). | `parted -s /dev/sda mklabel gpt`                |
| `-m`             | Muestra salida en formato máquina-legible.      | `parted -m /dev/sda print`                      |
| `mkpart`         | Crea una nueva partición.                       | `parted /dev/sda mkpart primary ext4 1MiB 100%` |
| `rm`             | Elimina una partición.                          | `parted /dev/sda rm 1`                          |
| `resize`         | Redimensiona una partición.                     | `parted /dev/sda resize 1 1MiB 500MiB`          |