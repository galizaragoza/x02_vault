Configurar y gestionar dispositivos mapeados (device mapper).

```
dmsetup [opciones] comando [args]
```

| **Parámetro**   | **Función**              | **Ejemplo**                                                  |
| ----------- | -------------------- | -------------------------------------------------------- |
| `create`    | Crea dispositivo.    | `dmsetup create test --table '0 100 linear /dev/sda1 0'` |
| `remove`    | Elimina dispositivo. | `dmsetup remove test`                                    |
| `status`    | Muestra estado.      | `dmsetup status test`                                    |
| `info`      | Muestra info.        | `dmsetup info test`                                      |
| `--verbose` | Modo verbose.        | `dmsetup --verbose create test`                          |
