Editor de flujo de texto para procesar y transformar datos en pipelines Unix, como filtrado, sustitución y edición no interactiva.

```
sed [opciones] 'comando' [archivo_entrada] > [archivo_salida]
```

```sh
sed 's/.*PATRON: /REEMPLAZO/' archivo.txt
## sed 'sustituye todos los chars hasta el PATRON y lo reemplaza por: NADA'
```

| **Parámetro** | **Función**                                      | **Ejemplo de Sintaxis**               |
| ------------- | ------------------------------------------------ | ------------------------------------- |
| -n            | Suprime salida automática; solo imprime con 'p'. | `sed -n 's/old/new/p' file.txt`       |
| -e            | Ejecuta múltiples expresiones.                   | `sed -e 's/old/new/' -e 'd' file.txt` |
| -f            | Lee comandos de un archivo.                      | `sed -f script.sed file.txt`          |
| -i            | Edita archivo in-place (sobreescribe original).  | `sed -i 's/old/new/g' file.txt`       |
| -r            | Usa expresiones regulares extendidas (ERE).      |                                       |
| g (flag en s) | Sustitución global en línea.                     | `sed 's/old/new/g' file.txt`          |
| p (comando)   | Imprime líneas coincidentes.                     | `sed -n '/pattern/p' file.txt`        |
| d (comando)   | Borra líneas coincidentes.                       | `sed '/pattern/d' file.txt`           |


# Protips
```
sed 's/old/new/g' file.txt
```
Reemplaza "old" por "new" globalmente

