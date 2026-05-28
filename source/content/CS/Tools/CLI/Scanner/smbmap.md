# ¿Qué es SMBMap?
SMBMap es una herramienta de enumeración y exploración de recursos compartidos SMB (Server Message Block) escrita en Python. Permite a los profesionales de ciberseguridad descubrir, listar y acceder a recursos compartidos en redes Windows de manera eficiente.
### Funciones Principales
1. **Enumeración de recursos compartidos** sin necesidad de credenciales
2. **Listado de archivos y directorios** en recursos compartidos
3. **Búsqueda recursiva** de archivos específicos
4. **Descarga/Subida** de archivos desde/hacia recursos compartidos
5. **Ejecución remota** de comandos (cuando hay credenciales válidas)
6. **Análisis de permisos** en recursos compartidos
### Utilización
```
smbmap [-h] [-H HOST] [-u USER] [-p PASS] [-d DOMAIN] [-P PORT] [OPCIONES]
```

|                  |                                             |                                                                                                   |
| ---------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| `-H`             | Escaneo básico sin credenciales             | smbmap -H 192.168.1.100                                                                           |
| `-u`, `-p`, `-d` | Escaneo autenticado (user, pass, dominio)   | smbmap -H 192.168.1.100 -u usuario -p contraseña -d dominio                                       |
| `-r`             | Listar contenido de recurso específico $^1$ | smbmap -H 192.168.1.100 -u usuario -p contraseña -r C$                                            |
| `-R`             | Búsqueda recursiva de archivos              | smbmap -H 192.168.1.100 -u usuario -p contraseña -R CarpetaCompartida --search "*.txt"            |
| `--search`       | Define patrón de búsqueda                   | smbmap -H 192.168.1.100 -u usuario -p contraseña -R CarpetaCompartida --search "*.txt"            |
| `--download`     | Define ruta del archivo a descargar         | smbmap -H 192.168.1.100 -u usuario -p contraseña --download "CarpetaCompartida/documento.pdf"     |
| `--upload`       | Define ruta del archivo a descargar         | smbmap -H 192.168.1.100 -u usuario -p contraseña --upload shell.exe "CarpetaCompartida/shell.exe" |
| `-x`             | Ejecución remota de comandos                | smbmap -H 192.168.1.100 -u usuario -p contraseña -x "whoami"                                      |
| `-iL`            | Escaneo del rango dado de IPs               | smbmap -iL lista_ips.txt -u guest -p ""                                                           |
| `-P`             | Define puerto SMB (default 445)             | smbmap -H 192.168.1.100 -P 4455 -u usuario -p contraseña                                          |
| `-v`             | Modo verboso                                | smbmap -H 192.168.1.100 -u usuario -p contraseña -v                                               |
|                  |                                             |                                                                                                   |
