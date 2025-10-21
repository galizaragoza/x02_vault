# ¿Qué es SMBClient?
**SMBClient** es un cliente SMB/CIFS multiplataforma que forma parte del proyecto Samba. Permite interactuar con recursos compartidos de red Windows/Linux de manera similar a un cliente FTP, proporcionando una interfaz de línea de comandos para operaciones de archivos y directorios.
## Funciones Principales
1. **Conexión interactiva** a recursos compartidos SMB
2. **Transferencia de archivos** (upload/download)
3. **Navegación de directorios** remotos
4. **Ejecución de comandos** remotos via Windows services
5. **Enumeración de recursos** compartidos
6. **Operaciones de archivos** (crear, eliminar, renombrar)

``` bash
smbclient [OPCIONES] //servidor/recurso [contraseña] [OPCIONES]
```

## Parámetros

|                |                                |                                                                                       |
| -------------- | ------------------------------ | ------------------------------------------------------------------------------------- |
| `-U`           | Identificarse con credenciales | smbclient //192.168.1.100/SharedDocs -U usuario%contraseña                            |
| `-U`           | Conexión con dominio           | smbclient //192.168.1.100/C$ -U dominio/usuario%contraseña                            |
| `-N`           | Conexión anónima               | 2                                                                                     |
| `-L`           | Listar recursos compartidos    | smbclient -L 192.168.1.100 -N                                                         |
| `-p`           | Definir puerto(default 445)    | smbclient //192.168.1.100/SharedDocs -p 4455 -U usuario%contraseña                    |
| `-c`           | Ejecutar comando y salir       | smbclient //192.168.1.100/C$ -U usuario%contraseña -c "dir"                           |
| `-d`           | Verbosidad (1-10)              | smbclient //192.168.1.100/SharedDocs -U usuario%contraseña -d 3                       |
| `-A`           | Usar archivo de config         | smbclient //192.168.1.100/SharedDocs -A archivo.auth                                  |
| `--pw-nt-hash` | Conexión con hash NTLM         | smbclient //192.168.1.100/C$ -U usuario --pw-nt-hash AAD3B435B51404EEAAD3B435B51404EE |
|                |                                |                                                                                       |

