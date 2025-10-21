### ¿Qué es Path Trasversal?
Path Trasversal, también conocido como Directory Trasversal es una vulnerabilidad que permite al atacante que la explota leer archivos del servidor que esta corriendo una aplicación a voluntad, tales como:
- Código fuente e información de la aplicación
- Credenciales para servicios del back-end
- Información sensible de la propia máquina
En algunos el atacante podría incluso modificar estos archivos de forma arbitraria, tomando control del servidor modificando el funcionamiento del código, haciendo defacement...
##### POC
Imaginemos una tienda virtual que muestra al cliente imágenes de sus productos con el siguiente código:
```HTML
<img src="/loadImage?filename=218.png">
```
El parámetro filename busca el archivo en el directorio `/var/www/images/` y lo muestra, de manera que ese código busca `/var/www/images/218.png`. En caso de una sanitización insuficiente, el atacante podría aprovecharse y hacer uso del componente de path `../`, que indica a la terminal que queremos ir al directorio "superior" al actual. Encadenando varios de estos componentes, podríamos retroceder hasta la raíz y consultar el contenido de todo tipo de archivos sensibles.
```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```
### Fuentes/artículos
[PortSwigger sobre Path Trasversal](https://portswigger.net/web-security/file-path-traversal)
[Bypasses de PATT](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal#methodology)
