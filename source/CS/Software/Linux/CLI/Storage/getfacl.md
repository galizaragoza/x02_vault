Muestra las listas de control de acceso (ACL) de archivos y directorios en sistemas Linux con soporte POSIX ACL.

```zsh
getfacl [opciones] archivo_o_directorio
```

| **Parámetro**     | **Función**                                          | **Ejemplo de Sintaxis**            |
| ------------- | ------------------------------------------------ | ------------------------------ |
| (ninguno)     | Muestra ACL completa en formato largo            | `getfacl /etc/shadow`          |
| -p            | Formato corto (solo entradas ACL)                | `getfacl -p secret.txt`        |
| -n            | Muestra UID/GID numéricos (sin resolver nombres) | `getfacl -n /home/user`        |
| -R            | Recursivo en directorios                         | `getfacl -R /var/www`          |
| -s            | Omite cabecera y solo muestra entradas ACL       | `getfacl -s file.txt`          |
| --tabular     | Salida en formato tabla (más legible)            | `getfacl --tabular config.php` |
| --omit-header | Sin cabecera (útil en scripts)                   | `getfacl --omit-header /tmp`   |
| -             | Lee nombres de archivo desde stdin               | `find /etc -type f             |