shred sobreescribe ficheros (o dispositivos de bloque) con datos aleatorios para dificultar su recuperación forense. Por defecto realiza 3 pasadas. Es importante entender sus limitaciones: no garantiza borrado seguro en sistemas de ficheros con journaling (ext4, btrfs, XFS), dispositivos flash/SSD (wear leveling), RAID, sistemas en red o copias de seguridad automáticas. Para SSDs, el comando `blkdiscard` o el borrado seguro ATA es más adecuado.

```
shred [opciones] fichero...
```

---

## Opciones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-n <N>` / `--iterations=<N>` | Número de pasadas de sobreescritura. Por defecto: 3. | `shred -n 7 fichero.txt` |
| `-z` / `--zero` | Añade una pasada final con ceros para ocultar que se usó shred. | `shred -z fichero.txt` |
| `-u` / `--remove[=CÓMO]` | Elimina el fichero después de sobreescribir. Opciones `CÓMO`: `unlink` (por defecto), `wipe` (borra nombre del dir), `wipesync` (wipe con sync a disco). | `shred -u fichero.txt` |
| `-f` / `--force` | Cambia permisos del fichero si es necesario para poder escribirlo. | `shred -f fichero_readonly.txt` |
| `-v` / `--verbose` | Muestra el progreso de cada pasada. | `shred -v fichero.txt` |
| `-x` / `--exact` | No redondea el tamaño del fichero al múltiplo de bloque siguiente. | `shred -x fichero.txt` |
| `-s <N>` / `--size=<N>` | Sobreescribe solo los primeros N bytes. Acepta sufijos K, M, G. | `shred -s 1M fichero` |
| `--random-source=<fichero>` | Usa el fichero indicado como fuente de datos aleatorios (en lugar de `/dev/urandom`). | `shred --random-source=/dev/urandom fichero` |

---

## Casos de uso comunes

```bash
# Sobreescritura estándar (3 pasadas)
shred fichero_sensible.txt

# 7 pasadas + pasada final con ceros + eliminar
shred -n 7 -zu fichero_sensible.txt

# Verbose para ver progreso
shred -v -n 3 -zu fichero.txt

# Sobreescribir partición entera (requiere root)
sudo shred -v -n 3 /dev/sdb1

# Sobreescribir disco entero
sudo shred -v -n 1 -z /dev/sdb

# Con fuente aleatoria explícita
shred -n 3 --random-source=/dev/urandom fichero.txt
```

> **Alternativas modernas para borrado seguro:** `wipe`, `secure-delete` (`srm`), o para SSDs: `blkdiscard -s /dev/sdX` o `hdparm --security-erase`.
