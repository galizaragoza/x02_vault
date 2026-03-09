```
fdisk [opciones] dispositivo
```

| **Parámetro (Flag)** | **Función**                                          | **Ejemplo de Sintaxis**                       |
| ---------------- | ------------------------------------------------ | ----------------------------------------- |
| `-l`             | Lista particiones de todos los discos.           | `fdisk -l`                                |
| `-u`             | Muestra tamaños en sectores.                     | `fdisk -u /dev/sda`                       |
| `n`              | Crea nueva partición (modo interactivo).         | `fdisk /dev/sda` luego `n` para nueva.    |
| `d`              | Elimina partición (modo interactivo).            | `fdisk /dev/sda` luego `d` para eliminar. |
| `p`              | Imprime tabla de particiones (modo interactivo). | `fdisk /dev/sda` luego `p` para print.    |