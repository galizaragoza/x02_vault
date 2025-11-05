# Comandos básicos
### grep
#### Protip
```bash
grep -P '^(?=.*[A-Z])(?=.*[0-9]).{8,}$'
```
	^: Inicio de línea.
	(?=.*[A-Z]): Al menos una mayúscula.
	(?=.*[0-9]): Al menos un dígito.
	$: Fin de línea.
### head 
```
  -c, --bytes=[-]NUM       print the first NUM bytes of each file;
                             with the leading '-', print all but the last
                             NUM bytes of each file

  -n, --lines=[-]NUM       print the first NUM lines instead of the first 10;
                             with the leading '-', print all but the last
                             NUM lines of each file
```

# Comandos específicos distro
### APT
 ![[apt-cs.png|360x450]]
