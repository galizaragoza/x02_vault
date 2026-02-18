Imprime o controla el buffer de mensajes del kernel.

```
dmesg [opciones]
```

| **Parámetro** | **Función**                                | **Ejemplo**          |
| --------- | -------------------------------------- | ---------------- |
| `-c`      | Limpia buffer tras imprimir.           | `dmesg -c`       |
| `-s`      | Usa buffer de tamaño específico.       | `dmesg -s 16384` |
| `-n`      | Establece nivel de logging en consola. | `dmesg -n 1`     |
