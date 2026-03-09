(EL RÁPIDO)
```
stegseek -sf archivo [opciones] wordlist
```

| Parámetro                 | Función                                      | Ejemplo de sintaxis                     |
| ------------------------- | -------------------------------------------- | --------------------------------------- |
| `-sf, --stegfile FILE`    | Archivo estego a crackear/extraer            | `stegseek -sf image.jpg`                |
| `-p, --passphrase PASS`   | Password conocida para extraer               | `stegseek -sf image.jpg -p "secret"`    |
| `-xf, --extractfile FILE` | Archivo de salida para datos extraídos       | `stegseek -sf image.jpg -xf secret.txt` |
| `-t, --threads N`         | Número de threads (default 4)                | `stegseek -sf image.jpg -t 8`           |
| `-f, --force`             | Sobrescribir archivos existentes             | `stegseek -sf image.jpg -f`             |
| `-v, --verbose`           | Salida detallada                             | `stegseek -sf image.jpg -v`             |
| `-q, --quiet`             | Ocultar métricas de performance              | `stegseek -sf image.jpg -q`             |
| `-s, --skipdefault`       | Omitir guesses por defecto (vacío, filename) | `stegseek -sf image.jpg -s`             |
| `-n, --nocolor`           | Desactivar colores                           | `stegseek -sf image.jpg -n`             |
| `-c, --continue`          | Continuar tras encontrar resultado           | `stegseek -sf image.jpg -c`             |
| `-a, --accessible`        | Output simplificado para screen readers      | `stegseek -sf image.jpg -a`             |