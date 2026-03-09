Ordena líneas de texto en archivos o entrada estándar (ascendente por defecto).

```
sort [opciones] [archivo(s)]
```

| **Parámetro**        | **Función**                            | **Ejemplo de Sintaxis**                 |
| ---------------- | ---------------------------------- | ----------------------------------- |
| `-o archivo`     | Guarda salida en archivo           | `sort ips.txt -o ips_ordenadas.txt` |
| `-r`             | Orden inverso (descendente)        | `sort -r puertos.txt`               |
| `-n`             | Orden numérico                     | `sort -n versiones.txt`             |
| `-k campo`       | Ordena por columna específica      | `sort -t: -k3 /etc/passwd`          |
| `-t delimitador` | Define separador de campos         | `sort -t, -k2 ips.csv`              |
| `-u`             | Elimina duplicados (únicos)        | `sort -u usuarios.txt`              |
| `-f`             | Ignora mayúsculas/minúsculas       | `sort -f dominios.txt`              |
| `-V`             | Orden versión natural (1.10 > 1.2) | `sort -V paquetes.txt`              |
| `-R`             | Orden aleatorio                    | `sort -R wordlist.txt`              |