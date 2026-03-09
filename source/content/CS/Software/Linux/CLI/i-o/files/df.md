|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-h` o `--human-readable`|Muestra tamaños en formato legible|`df -h`|
|`-T` o `--print-type`|Muestra el tipo de sistema de archivos|`df -T`|
|`-t` o `--type=`|Filtra por tipo de sistema de archivos|`df -t ext4`|
|`-x` o `--exclude-type=`|Excluye tipos de sistemas de archivos|`df -x tmpfs`|
|`-i` o `--inodes`|Muestra información de inodos en lugar de bloques|`df -i`|
|`-a` o `--all`|Incluye sistemas de archivos ficticios|`df -a`|
|`--output=`|Selecciona columnas específicas para mostrar|`df --output=source,fstype,size`|

# Ejemplos
```sh
# Ver espacio en formato legible
df -h

# Ver espacio con tipos de sistemas de archivos
df -hT

# Ver solo sistemas de archivos ext4
df -h -t ext4

# Ver información de inodos
df -hi

# Ver columnas específicas
df --output=source,fstype,size,used,avail,pcent,target -h
```