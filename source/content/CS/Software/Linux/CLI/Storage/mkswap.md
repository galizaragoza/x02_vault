Configurar un área de swap en un dispositivo o archivo.

```
mkswap [opciones] dispositivo
```

| **Parámetro** | **Función**                        | **Ejemplo**                     |
| --------- | ------------------------------ | --------------------------- |
| `-c`      | Verifica sectores defectuosos. | `mkswap -c /dev/sdb1`       |
| `-L`      | Asigna etiqueta.               | `mkswap -L swap1 /dev/sdb1` |
| `-p`      | Especifica tamaño de página.   | `mkswap -p 4096 /dev/sdb1`  |
| `-U`      | Asigna UUID.                   | `mkswap -U clear /dev/sdb1` |
| `-v`      | Especifica versión de swap.    | `mkswap -v1 /dev/sdb1`      |
