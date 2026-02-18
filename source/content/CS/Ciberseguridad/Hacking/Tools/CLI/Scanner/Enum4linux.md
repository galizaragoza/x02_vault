
```bash
enum4linux [opciones] <IP_objetivo>
```

|      |                                                              |                                                            |
| ---- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| `-U` | Lista los usuarios                                           | enum4linux -U 192.168.1.200                                |
| `-S` | Lista los recursos compartidos (shares)                      | enum4linux -U 192.168.1.200                                |
| `-P` | Extrae la política de contraseñas                            | enum4linux -P 192.168.1.200                                |
| `-G` | Enumera los grupos y los miembros de los mismos              | enum4linux -G 192.168.1.200                                |
| `-o` | Muestra información sobre el sistema operativo auditado      | enum4linux -G 192.168.1.200                                |
| `-a` | Combina `-U`, `-S`, `-G`, `-P`, `-r`, `-o`, `-n` y `-i`      | enum4linux -a 192.168.1.200                                |
| `-u` | Especifica un usuario para intentar autenticar               | enum4linux -u msfadmin -p msfadmin -U 192.168.1.200        |
| `-p` | Especifica una passwd para intentar autenticar               | enum4linux -u msfadmin -p msfadmin -S 192.168.1.200        |
| `-i` | En caso de existir, obtiene información sobre la impresora   | enum4linux -i 192.168.1.200                                |
| `-r` | Habilita la enumeración de usuarios mediante RID cycling (1) | enum4linux -r 192.168.1.200                                |
| `-R` | Designa rangos personalizados de RIDs para el cycling        | enum4linux -r -R 500-600,1000-1200,2000-2050 192.168.1.200 |
| `-v` | Activa el modo verbose                                       | enum4linux -v -U 192.168.1.200                             |
| `-n` | Realiza escaneo NetBIOS (2)                                  | enum4linux -n 192.168.1.200                                |
|      |                                                              |                                                            |
	1 --> El RID cycling es una técnica para enumerar cuentas de usuario en un dominio Windows aprovechando sesiones nulas. Esta técnica se basa en que el Identificador de Seguridad (SID) del controlador de dominio puede obtenerse a través de LDAP, y luego se añaden diferentes RID a este SID para probar qué combinaciones corresponden a cuentas de usuario válidas
	2 --> Enumera información básica como el nombre de la máquina, el dominio o workgroup al que pertenece, y otros detalles disponibles a través del protocolo NetBIOS sobre SMB. Es útil para recopilar datos iniciales sobre la identidad y configuración de red del sistema sin autenticación. Depende de que el puerto 137/UDP o 139/TCP esté abierto y NetBIOS esté habilitado.