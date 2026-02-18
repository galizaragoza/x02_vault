### ¿Qué es el file upload?
Una vulnerabilidad de file upload o insecure file upload se da cuando un servidor web permite la subida de archivos a su sistema sin la sanitización pertinente a nivel de tipo de archivo, nombre, contenido o tamaño. Esto abre muchos vectores de ataque ya que esta subida de archivos no restringida, llevada al extremo para poner un ejemplo, permitiría sencillamente subir un archivo .php con una reverse shell, lo cual comprometería por completo el sistema. 
![[file-upload-mindmap.png]]

Hay infinidad de técnicas de ofuscación y camuflaje de instrucciones arbitrarias y/o maliciosas para incluir en todo tipo de archivos, ya sea ocultar comandos o XSS en los metadatos de una imagen, poner dobles extensiones (ejemplo.jpg.php) cambiar el Content-Type header...
### Bypass en contenido
Si la aplicación en cuestión no está filtrando por extensión si no por contenido, se la puede engañar subiendo un archivo cuya extensión es, por ejemplo `.png`, pero cambiando el contenido por código, el servidor podría aceptar la imagen pero una vez intente procesarla la interpretará como código, se ve genial este fenómeno en [este vídeo](https://youtu.be/rAyws427ZW8?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR).
Esto se da cuando el método de sanitización consiste en comprobar el contenido del archivo en cuestión en lugar de su extensión, esto se puede detectar si, por ejemplo una página dice que solo admine `.png`, pero conseguimos subir un archivp `.jpeg`.
#### Magic Numbers
### Cheats
```
%00
\x00
```
Null bytes
### Artículos
[File upload vulnerabilities (PortSwigger)](https://portswigger.net/web-security/file-upload)
[PayLoadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files#methodology)
[Demostración upload bypass](https://youtu.be/rAyws427ZW8?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)