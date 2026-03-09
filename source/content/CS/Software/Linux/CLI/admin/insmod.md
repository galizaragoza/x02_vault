`insmod` inserta módulos del kernel en el kernel de Linux en tiempo de ejecución. Carga módulos individuales sin resolver dependencias automáticamente.

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|(sin parámetros)|Inserta el módulo especificado|`insmod mi_modulo.ko`|
|`-f`|Fuerza la carga del módulo (peligroso)|`insmod -f mi_modulo.ko`|
|`-s`|Envía mensajes al syslog en lugar de stdout|`insmod -s mi_modulo.ko`|
|`-v` o `--verbose`|Muestra más información durante la carga|`insmod -v mi_modulo.ko`|
|`-V` o `--version`|Muestra la versión del programa|`insmod -V`|
|`-h` o `--help`|Muestra ayuda del comando|`insmod -h`|
