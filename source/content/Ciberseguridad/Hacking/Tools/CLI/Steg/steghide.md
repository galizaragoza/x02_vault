
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