### ¿Qué es steghide?
**Steghide** es una herramienta de esteganografía de código abierto que permite ocultar información secreta dentro de archivos de imagen (JPEG, BMP), audio (WAV, AU) y otros formatos, sin alterar perceptiblemente la apariencia o calidad del archivo portador.

**Funciones Principales**
1. **Ocultar datos** en archivos portadores
2. **Extraer datos** ocultos previamente insertado
3. **Obtener información** sobre archivos que contienen datos ocultos
4. **Cifrar datos** con diferentes algoritmos (AES, Rijndael, etc.)
5. **Comprimir datos** antes de ocultarlos
```
steghide [comando] [opciones] [archivos]
```


|           |                                               |                                                                    |
| --------- | --------------------------------------------- | ------------------------------------------------------------------ |
| `embed`   | `-cf`-->Carrier file<br>`-ef`-->Embedded file | steghide embed -cf imagen.jpg -ef archivo_secreto.txt              |
| `extract` | Extraer datos ocultos                         | steghide extract -sf imagen_modificada.jpg                         |
| `info`    | Revela información del archivo                | steghide info imagen_sospechosa.jpg                                |
| `-e`      | Define algoritmo de cifrado$^1$               | steghide embed -cf foto.jpg -ef datos.txt -e aes128                |
| `-p`      | Define la contraseña                          | steghide embed -cf audio.wav -ef keylog.txt -p "miClaveSecreta123" |
| `-z`      | Define nivel de compresión<br>(1-9)           | steghide embed -cf documento.bmp -ef database.db -z 9              |
| `-f`      | Forzar operación                              | steghide embed -cf imagen.jpg -ef payload.exe -f                   |
| `-v`      | Modo verbose                                  | steghide info archivo_sospechoso.wav -v                            |
|           |                                               |                                                                    |
> 1 --> none, aes128, aes192, aes256, rijndael128, rijndael192, rijndael256