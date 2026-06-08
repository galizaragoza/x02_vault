`-p` o `--primary`: Selección primaria (selección con el mouse, middle-click)    
`-s` o `--secondary`: Selección secundaria (rara vez usada)    
`-b` o `--clipboard`: Portapapeles tradicional (Ctrl+C/Ctrl+V)    
`-k` o `--keep`: Mantiene la selección original después de acceder a ella

## Opciones

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`-a`, `--append`|Añade a la selección existente|`xsel -b -a`|
|`-c`, `--clear`|Limpia la selección|`xsel -b -c`|
|`-d`, `--delete`|Elimina la selección actual|`xsel -p -d`|
|`-i`, `--input`|Establece la selección desde stdin|`echo "texto" \| xsel -b -i`|
|`-o`, `--output`|Muestra la selección por stdout|`xsel -b -o`|
|`-l`, `--logfile`|Especifica archivo de log|`xsel -l /tmp/xsel.log`|
|`--display`|Especifica pantalla X a usar|`xsel --display :0`|
|`-t`, `--selectionTimeout`|Timeout para obtener selección (ms)|`xsel -t 5000`|
|`-f`, `--follow`|Sigue cambios en la selección|`xsel -b -f`|
|`-n`, `--nodetach`|No se separa del terminal en modo follow|`xsel -b -f -n`|

## Modos de operación (mutuamente excluyentes)

|Modo|Función|Ejemplo|
|---|---|---|
|Lectura|Lee la selección (por defecto)|`xsel -b`|
|`-i`, `--input`|Establece la selección|`xsel -b -i`|
|`-c`, `--clear`|Limpia la selección|`xsel -p -c`|
|`-d`, `--delete`|Lee y luego limpia|`xsel -b -d`|
|`-a`, `--append`|Añade a la selección|`xsel -b -a`|

## Protips
### Copiar al portapapeles
```
echo "texto" | xsel -b -i
```

### Pegar desde el clipboard
```
xsel -b -o
```