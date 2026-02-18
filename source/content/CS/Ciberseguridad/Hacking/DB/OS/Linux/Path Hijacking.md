Path Hijacking es un vector de escalada que puede explotar cuando el usuario en el que estamos atacando la máquina puede de alguna manera manipular el `PATH` del sistema. El `PATH` es el orden en el que el OS "pregunta" de donde debe ejecutar un binario.
Si se escribe un comando cualquiera, por ejemplo `ls`, el sistema va buscando en orden por el `PATH` hasta llegar a dicho binario, por ejemplo

```
/root/.cargo/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games
```

La idea del Path Hijacking es manipular el path y añadirle un directorio que podamos controlar, y en él crear un binario que sirva para escalar privilegios

```
/tmp/maliciousDir:/root/.cargo/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games
```

Si en ese ejemplo escribimos en maliciousDir un script que lance una shell privilegiada al ejecutar `ls` ya estaría completada la escalada.
	La forma de llevarlo acabo sería crear un archivo llamado `ls.sh` en el directorio que controlamos (`/maliciousdir`) y en el incluir algún tipo de código que permita escalar privilegios(**`chmod 777`** para hacerlo ejecutable)