> LinPEAS (Linux Privilege Escalation Awesome Script) es un script diseñado para enumerar todos los posibles vectores de escalada de privilegios una vez ya estamos dentro de un sistema Linux/Unix/macOS. Busca de forma automática configuraciones erróneas, vulnerabilidades y otras oportunidades con las que escalar privilegios.
##### [Repositorio oficial](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS)
##### [Cheatsheet instalación](https://afsh4ck.gitbook.io/ethical-hacking-cheatsheet/post-explotacion/escalada-de-privilegios/escalada-de-privilegios-en-linux/linpeas)

**Funciones principales**
- **Enumeración del sistema**: Recopila información sobre sistema operativo, kernel, usuarios, grupos, procesos y servicios.
- **Detección de vulnerabilidades**: Identifica archivos SUID/SGID, permisos débiles, configuraciones sudo incorrectas y exploits conocidos.
- **Búsqueda de credenciales**: Encuentra contraseñas en archivos de configuración, historiales y scripts.
- **Análisis de permisos**: Revisa permisos de archivos críticos (e.g., /etc/passwd) y directorios escribibles.
- **Información legible**: Genera salida coloreada para destacar vectores de escalación (rojo/amarillo = alta probabilidad).
```
./linpeas.sh [opciones]
```
### Comandos de instalación
#### Desde el repositorio de GitHub
``` bash
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh
```

|      |                                                                                 |                                          |
| ---- | ------------------------------------------------------------------------------- | ---------------------------------------- |
| `-a` | Ejecuta **todas** las comprobaciones                                            | ./linpeas.sh -a > linpeas_output.txt     |
| `-o` | Selecciona **módulos específicos**                                              | ./linpeas.sh -o system_information,users |
| `-f` | Analiza un sistema de archivos montado sin ejecutar en el sistema objetivo.$^1$ | ./linpeas.sh -f /mnt/firmware            |
| `-q` | Modo silencioso, reduce salida en consola para **stealth**                      | ./linpeas.sh -q > /dev/shm/linpeas.txt   |
| `-s` | Incluye sugerencias de **exploits** basadas en la **versión del kernel**        | ./linpeas.sh -s                          |
|      |                                                                                 |                                          |
	1 --> El parámetro -f en LinPEAS permite analizar un sistema de archivos montado (e.g., un directorio, imagen de disco o firmware) **sin ejecutarlo directamente en el sistema objetivo**. Esto es útil en escenarios de pentesting o análisis forense donde no tienes acceso directo al sistema o deseas evitar ejecutar scripts en la máquina víctima para minimizar detección o impacto.