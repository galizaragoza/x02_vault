>CrackMapExec es una herramienta de **post explotación** para evaluar la seguridad de un equipo Windows/Active Directory al que hemos ganado acceso. Se considera una navaja suiza por su amplia gama de herramientas y posibilidades, entre ellas:

**Enumeración de Active Directory:** Identificar dominios, usuarios, grupos, sesiones, políticas de contraseñas y recursos compartidos
**Ataques de credenciales:** Realiza atraques de fuerza bruta, spraying y pash-the-hash para autenticación
**Ejecución remota:** Ejecuta comandos o scripts en las máquinas víctima vía PowerShell, WMI, SMB o PSExec
**Movimiento lateral:** Facilita la propagación entre equipos comprometidos mediante pass-the-hash y token impersonation
**Post-explotación:** Establece persistencia, recolecta información de red y aplicaciones y despliega agentes
``` bash
crackmapexec smb 192.168.1.105 -u Administrator -p 'P@ssw0rd' -x 'whoami' --exec-method smbexec
```
## Parámetros

|                                                                                    |     |
| ---------------------------------------------------------------------------------- | --- |
| `-u` especifica el username o diccionario de usernames                             |     |
| `-p` define la pass o diccionario de passes                                        |     |
| `-H` usa hashes NTLM para autenticación (pass-the-hash).                           |     |
| `-x` ejecuta comandos a través de cmd.exe (requiere privilegios de administrador). |     |
| `-X` ejecuta comandos PowerShell (requiere privilegios de administrador).          |     |
| `--exec-method` selecciona método de ejecución (wmiexec, atexec, smbexec, mmcexec) |     |
| `--shares` enumera carpetas compartidas                                            |     |
| `--users` enumera usuarios del sistema o dominio                                   |     |
| `--groups` enumera grupos del dominio                                              |     |
| `--no-bruteforce` evita fuerza bruta, prueba combinaciones específicas             |     |
| `--pass-pol` muestra políticas de contraseñas                                      |     |
| `-M` usa módulos específicos                                                       |     |
