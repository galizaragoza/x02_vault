mv mueve o renombra ficheros y directorios. A diferencia de `cp`, no copia los datos en el mismo sistema de ficheros (solo actualiza los metadatos del directorio), lo que lo hace instantáneo para movimientos locales. Entre sistemas de ficheros distintos sí copia los datos y luego elimina el origen. No requiere `-r` para directorios.

```
mv [opciones] origen destino
mv [opciones] origen... directorio
```

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` / `--interactive` | Pregunta antes de sobreescribir un fichero existente en destino. | `mv -i fichero destino/` |
| `-n` / `--no-clobber` | No sobreescribe ficheros existentes en destino. Si el destino existe, no hace nada. | `mv -n fichero destino/` |
| `-f` / `--force` | No pregunta antes de sobreescribir (anula `-i`). | `mv -f origen destino` |
| `-u` / `--update` | Solo mueve si el origen es más nuevo que el destino, o si el destino no existe. | `mv -u *.log /archivo/` |
| `-b` | Crea backup del fichero destino antes de sobreescribirlo. Sufijo por defecto: `~`. | `mv -b config.conf /etc/` |
| `--backup[=CONTROL]` | Como `-b` con control del método: `numbered`, `existing`, `simple`, `none`. | `mv --backup=numbered fichero destino/` |
| `-S <sufijo>` / `--suffix=<sufijo>` | Define el sufijo de backup. | `mv -b -S .bak config /etc/` |
| `-t <dir>` / `--target-directory=<dir>` | Mueve todos los orígenes al directorio indicado. | `mv -t /destino/ *.txt` |
| `-T` / `--no-target-directory` | Trata el destino como fichero, no como directorio. | `mv -T origen_dir destino_dir` |
| `-v` / `--verbose` | Muestra cada operación realizada. | `mv -v *.log /archivo/` |
| `--strip-trailing-slashes` | Elimina barras finales de los argumentos origen. | `mv --strip-trailing-slashes dir/ destino/` |

---

## Casos de uso comunes

```bash
# Renombrar fichero
mv fichero.txt fichero_renombrado.txt

# Mover a directorio
mv fichero.txt /destino/

# Mover directorio (instantáneo en mismo filesystem)
mv proyecto/ /backup/

# Renombrar directorio
mv nombre_viejo/ nombre_nuevo/

# No sobreescribir existentes
mv -n *.jpg /fotos/

# Con confirmación antes de sobreescribir
mv -i fichero destino/

# Solo si el origen es más nuevo
mv -u logs/*.log /archive/

# Mover múltiples ficheros a directorio
mv -t /tmp/ file1 file2 file3

# Con backup del destino si existe
mv -b --suffix=.orig config.conf /etc/config.conf

# Verbose para ver qué se movió
mv -v /tmp/*.log /var/log/archive/
```
