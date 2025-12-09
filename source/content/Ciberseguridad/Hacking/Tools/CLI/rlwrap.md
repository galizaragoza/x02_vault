[rlwrap: When and how to use it](https://github.com/hanslub42/rlwrap?tab=readme-ov-file#when-and-how-to-use-it)

```
rlwrap [opciones] comando
```

| **Flag**        | **Función**                              | **Ejemplo**                                  |
| ----------- | ------------------------------------ | ---------------------------------------- |
| `-A`        | Soporte ANSI (colores + flechas)     | `rlwrap -A nc 10.10.10.5 443`            |
| `-c`        | Autocompletado de archivos           | `rlwrap -c nmap`                         |
| `-f file`   | Archivo personalizado de completado  | `rlwrap -f ~/.msf_words nc -lvnp 443`    |
| `-H file`   | Historial en archivo propio          | `rlwrap -H ~/.nc_hist nc 10.10.10.5 443` |
| `-pColor`   | Color del prompt (Red, Green, Blue…) | `rlwrap -pRed nc -lvnp 4444`             |
| `-r`        | Recuperar comandos previos al buffer | `rlwrap -r telnet 10.10.10.10`           |
| `-s N`      | Tamaño máximo del historial          | `rlwrap -s 20000 nc -lvnp 443`           |
| `-z filter` | Filtros predefinidos (sqlplus, etc.) | `rlwrap -z sqlplus sqlplus user/pass`    |