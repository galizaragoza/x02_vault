
```
hexdump [opciones] archivo
```

| **Parámetro** | **Función**                                         | **Ejemplo de uso**                   |
| --------- | ----------------------------------------------- | -------------------------------- |
| -C        | Formato canónico (hex + ASCII, 16 bytes/línea)  | hexdump -C malware.bin           |
| -v        | Muestra todas las líneas (no colapsa repetidas) | hexdump -v -C shellcode.bin      |
| -n length | Limita salida a N bytes                         | hexdump -n 64 -C payload.bin     |
| -s offset | Salta los primeros N bytes (skip)               | hexdump -s 0x100 -C firmware.bin |
| -e format | Formato personalizado                           | hexdump -e '4/4 "%08X "' file    |
| -x        | Solo hexadecimal (2 bytes por grupo)            | hexdump -x shellcode             |
| -d        | Solo decimal                                    | hexdump -d datos.bin             |
| -o        | Solo octal                                      | hexdump -o archivo               |
| -b        | Dump en bytes (octal, 1 byte)                   | hexdump -b raw_socket            |
| -c        | Solo caracteres ASCII                           | hexdump -c texto_oculto.bin      |
