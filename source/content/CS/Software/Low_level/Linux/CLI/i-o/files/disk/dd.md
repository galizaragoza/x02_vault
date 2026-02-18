Copiar y convertir datos raw.

```
dd [opciones]
```

[man dd](https://man7.org/linux/man-pages/man1/dd.1.html)

|Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`if=FILE`|Lee desde archivo.|`dd if=/etc/passwd`|
|`of=FILE`|Escribe en archivo.|`dd of=/tmp/output.bin`|
|`bs=BYTES`|Tamaño de bloque.|`dd bs=1M`|
|`conv=CONVS`|Aplica conversiones.|`dd conv=swab if=input of=output`|
|`skip=N`|Omite bloques entrada.|`dd skip=1 if=disk.img`|
|`seek=N`|Salta bloques salida.|`dd seek=100 of=disk.img`|
|`count=N`|Limita bloques.|`dd count=10 if=/dev/zero`|