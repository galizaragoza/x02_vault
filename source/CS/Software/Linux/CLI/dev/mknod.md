Crea archivos especiales en sistemas Unix-like, como dispositivos de bloque/carácter, FIFOs (named pipes) o sockets, para interactuar con hardware o procesos.
```
mknod [opciones] nombre tipo [major minor]
```

|Parámetro (Flag)|Función|Ejemplo de Sintaxis|
|---|---|---|
|`-m MODE`|Establece permisos (modo) del nodo creado.|`mknod -m 0666 /tmp/mypipe p`|
|`-Z CONTEXT`|Asigna contexto SELinux al nodo (útil en sistemas seguros).|`mknod -Z system_u:object_r:tmp_t:s0 /dev/mynode c 1 3`|
|`--help`|Muestra ayuda de uso.|`mknod --help`|
|`--version`|Muestra versión del comando.|`mknod --version`|