> Hydra es una herramienta cuya función principal es ejecutar ataques de diccionario a servicios como SSH, FTP, HTTP...

**Funciones principales:**
- **Ataques de fuerza bruta**: Prueba combinaciones de nombres de usuario y contraseñas para acceder a servicios.
- **Ataques de diccionario**: Usa listas predefinidas de usuarios y contraseñas para optimizar tiempo.
- **Soporte multi-protocolo**: Compatible con protocolos como FTP, SSH, HTTP, SMB, Telnet, etc.
- **Multi-host**: Permite atacar múltiples objetivos simultáneamente.
- **Automatización**: Usa archivos de texto para usuarios/contraseñas, agilizando pruebas.
```
hydra [opciones] <objetivo> <protocolo>
```

|      |                                                             |                                                          |
| ---- | ----------------------------------------------------------- | -------------------------------------------------------- |
| -f   | Finaliza el ataque al encontrar credenciales válidas        | hydra -f [protocolo://ip_o_dominio]                      |
| -l   | Especifica un sólo nombre de usuario                        | hydra -l admin 10.0.0.1 ftp                              |
| -L   | Proporciona una lista de usernames para sobre la que iterar | hydra -L usuarios.txt 10.0.0.1 ssh                       |
| -p   | Especifica una única contraseña                             | hydra -p [password] [protocolo://ip_o_dominio]           |
| -P   | Especifica una lista de contraseñas sobre la que iterar     | hydra -P [lista de passwords] [protocolo://ip_o_dominio] |
| -s   | Define el puerto a atacar                                   | hydra -s [puerto] [protocolo://ip_o_dominio]             |
| -t   | Define la cantidad de threads a usar en el ataque           | hydra -t [protocolo://ip_o_dominio]                      |
| -R   | restaura una sesión previamente crasheada                   | hydra -R                                                 |
| `-I` | Ignora la sesión almacenada (no espera 10s)                 |                                                          |
| -u   | solo itera sobre usuarios, no contraseñas                   | hydra -u [protocolo://ip_o_dominio]                      |
| -vV  | Modo verbose                                                |                                                          |
| -o   | Guarda el resultado en un archivo                           |                                                          |
|      |                                                             |                                                          |
## Cheats
```
hydra -L /lista/password.txt -P /lista/usernames.txt http-get://172.17.0.2:8080/recurso.ejemplo
```
Este comando sirve para hacer ataques a paneles de login HTTP Basic Auth
```
php,html,db,conf,config,txt,js,py,bak,conf,env,jar,csv,xml,md,pdf,zip,rar,jpg,jpeg,png,db,sql,log,json
```