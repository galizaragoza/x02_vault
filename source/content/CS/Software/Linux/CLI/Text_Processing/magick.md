`magick` es el punto de entrada principal para la suite ImageMagick 7, una herramienta multiplataforma para crear, editar, componer y convertir imágenes de mapa de bits. A diferencia de versiones anteriores, consolida las funcionalidades de las antiguas subherramientas (convert, identify, etc.) en un solo binario. Permite procesar una amplia gama de formatos y aplicar transformaciones complejas mediante una interfaz de línea de comandos altamente granular.

### Operadores de Edición de Imagen

Estos parámetros modifican los datos de los píxeles de la imagen de entrada.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-adaptive-resize`|Redimensiona la imagen de forma adaptativa para minimizar el ruido.|`magick in.jpg -adaptive-resize 800x600 out.jpg`|
|`-crop`|Corta una región rectangular de la imagen según la geometría especificada.|`magick in.png -crop 100x100+10+10 out.png`|
|`-flop`|Crea una imagen reflejada horizontalmente (efecto espejo).|`magick in.jpg -flop out.jpg`|
|`-negate`|Invierte todos los colores de la imagen (negativo).|`magick in.png -negate out.png`|
|`-resize`|Cambia el tamaño de la imagen preservando o ignorando el aspect ratio.|`magick in.jpg -resize 50% out.jpg`|
|`-rotate`|Aplica una rotación en grados a la imagen.|`magick in.jpg -rotate 90 out.jpg`|
|`-sharpen`|Aplica un filtro de enfoque mediante un radio y desviación estándar.|`magick in.png -sharpen 0x1 out.png`|


### Ajustes de Contexto y Formato

Parámetros que definen cómo se interpreta la entrada o cómo se debe codificar la salida.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-background`|Define el color de fondo para operaciones de relleno o aplanado.|`magick -background blue out.png`|
|`-colorspace`|Convierte la imagen al espacio de color especificado (RGB, CMYK, Gray).|`magick in.jpg -colorspace Gray out.jpg`|
|`-density`|Establece la resolución (DPI) para la renderización de formatos vectoriales.|`magick -density 300 in.pdf out.png`|
|`-quality`|Define el nivel de compresión para formatos como JPEG o PNG.|`magick in.png -quality 85 out.jpg`|
|`-size`|Especifica las dimensiones de una imagen cruda o un canvas nuevo.|`magick -size 100x100 canvas:white out.png`|


### Subherramientas (Herencia de Versiones Anteriores)

Aunque se ejecutan mediante el comando `magick`, emulan el comportamiento de las herramientas clásicas de ImageMagick.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`identify`|Describe las características y metadatos de un archivo de imagen.|`magick identify -verbose image.jpg`|
|`mogrify`|Modifica la imagen original "in-place" en lugar de crear una nueva.|`magick mogrify -resize 800x600 *.jpg`|
|`montage`|Combina varias imágenes para crear una composición o mosaico.|`magick montage *.png collage.png`|
|`compare`|Compara matemáticamente la diferencia entre dos imágenes.|`magick compare img1.png img2.png diff.png`|