
```bash
stegcracker <archivo> [<wordlist>]
```

| Parámetro           | Función                                                    | Ejemplo de sintaxis                   |
| ------------------- | ---------------------------------------------------------- | ------------------------------------- |
| `<archivo>`         | Archivo estego (jpg, jpeg, bmp, wav, au)                   | `stegcracker image.jpg`               |
| `<wordlist>`        | Lista de passwords (opcional; usa rockyou.txt por defecto) | `stegcracker image.jpg rockyou.txt`   |
| `-o, --output FILE` | Archivo de salida para datos extraídos                     | `stegcracker image.jpg -o secret.txt` |
| `-t, --threads N`   | Número de threads (default 16)                             | `stegcracker image.jpg -t 32`         |
| `-h, --help`        | Muestra ayuda                                              | `stegcracker -h`                      |
| `--version`         | Muestra versión                                            | `stegcracker --version`               |
