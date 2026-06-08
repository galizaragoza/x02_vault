> Fcrackzip es una herramienta que permite hacer ataques de fuerza bruta o diccionario a archivos .zip
``` bash
fcrackzip [opciones] <archivo.zip>
```

|                          |                                                                                                 |                                                                |
| ------------------------ | ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| -u                       | Desbloquea el archivo si se encuentra la contraseña (evita falsos positivos)                    | fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt sample.zip |
| -D                       | Usa un archivo diccionario                                                                      | fcrackzip -D -p /usr/share/wordlists/rockyou.txt sample.zip    |
| -p diccionario.txt       | Especifica que archivo se usará como diccionario                                                |                                                                |
| -b                       | Indica que el ataque se realizará por fuerza bruta                                              | fcrackzip -b -c a -l 1-6 sample.zip                            |
| -c aA1!                  | Especifica que caracteres usar (a=minusculas, A=mayusculas, 1=numeros, !=caracteres especiales) | fcrackzip -b -c a1 -l 4-8 sample.zip                           |
| -l [longitud a crackear] | Especifica la longitud mínima y máxima de la contraseña para fuerza bruta                       | fcrackzip -b -c a -l 3-5 sample.zip                            |
| -p                       | Define una contraseña inicial o archivo de diccionario.                                         | fcrackzip -b -c a -p aaaa sample.zip                           |
| -v                       | Modo verbose                                                                                    |                                                                |
|                          |                                                                                                 |                                                                |
