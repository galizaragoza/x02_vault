| **Parámetro** | **Función**                                                            | **Ejemplo de Sintaxis**                      |
| ------------- | ---------------------------------------------------------------------- | -------------------------------------------- |
| `-selection`  | Define qué portapapeles usar (`primary`, `secondary`, `clipboard`).    | `cat file.txt \| xclip -selection clipboard` |
| `-i`          | Lee la entrada estándar hacia el portapapeles (por defecto).           | `xclip -i archivo.txt`                       |
| `-o`          | Imprime el contenido del portapapeles en la terminal.                  | `xclip -o -selection clipboard`              |
| `-t`          | Especifica el tipo de contenido (MIME type).                           | `xclip -t image/png -o > imagen.png`         |
| `-l`          | Número de veces que el contenido debe estar disponible antes de salir. | `xclip -l 1 archivo.txt`                     |