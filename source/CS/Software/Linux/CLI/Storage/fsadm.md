Administrar sistemas de archivos, como redimensionar.

```
fsadm [opciones] comando [args]
```

| **Parámetro**   | **Función**           | **Ejemplo**                            |
| ----------- | ----------------- | ---------------------------------- |
| `check`     | Verifica FS.      | `fsadm check /dev/sda1`            |
| `resize`    | Redimensiona FS.  | `fsadm resize /dev/sda1 10G`       |
| `--dry-run` | Simula ejecución. | `fsadm --dry-run resize /dev/sda1` |
| `--force`   | Fuerza operación. | `fsadm --force check /dev/sda1`    |
| `--verbose` | Modo verbose.     | `fsadm --verbose resize /dev/sda1` |
