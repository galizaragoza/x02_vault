# SMB
```
crackmap smb <host> <opciones>
```

| **Flag**               | **Función**                   | **Ejemplo**                |
| ------------------ | ------------------------- | ---------------------- |
| `-u`               | Usuario(s)                | `-u jdoe`              |
| `-p`               | Contraseña(s)             | `-p Password123`       |
| `-H`               | NTLM hash                 | `-H 2892d26e...aacc`   |
| `--sam`            | Volcar SAM                | `--sam`                |
| `--lsa`            | Volcar LSA Secrets        | `--lsa`                |
| `--ntds`           | Volcar NTDS.dit (DCSync)  | `--ntds=drs`           |
| `--shares`         | Enumerar shares           | `--shares`             |
| `--sessions`       | Sesiones activas          | `--sessions`           |
| `--loggedon-users` | Usuarios logueados        | `--loggedon-users`     |
| `--rid-brute`      | Brute force RIDs          | `--rid-brute 10000`    |
| `--pass-pol`       | Política de contraseñas   | `--pass-pol`           |
| `-x`               | Ejecutar comando (SYSTEM) | `-x "whoami"`          |
| `--exec-method`    | Método ejecución          | `--exec-method atexec` |

# WinRM
| **Flag**               | **Función**           | **Ejemplo**                              |
| ------------------ | ----------------- | ------------------------------------ |
| `-u` / `-p` / `-H` | Credenciales      | `-u admin -p P@ss123`                |
| `-x`               | Ejecutar comando  | `-x "net user hacker P@ssw0rd /add"` |
| `--shell`          | Shell interactiva | `--shell`                            |

# LDAP
| **Flag**               | **Función**                 | **Ejemplo**                            |
| ------------------ | ----------------------- | ---------------------------------- |
| `-u` / `-p` / `-H` | Autenticación AD        | `-u jdoe@lab.local -p Password123` |
| `--users`          | Listar usuarios         | `--users`                          |
| `--groups`         | Listar grupos           | `--groups`                         |
| `--computers`      | Listar equipos          | `--computers`                      |
| `--admin-rights`   | Derechos admin          | `--admin-rights`                   |
| `--local-groups`   | Miembros grupos locales | `--local-groups`                   |
| `--bloodhound`     | Exportar BloodHound     | `--bloodhound -c All`              |

# MSSQL
| **Flag**          | **Función**       | **Ejemplo**            |
| ----------------- | ----------------- | ---------------------- |
| `-u` / `-p`       | Credenciales SQL  | `-u sa -p Password123` |
| `--local-auth`    | Auth Windows      | `--local-auth -H hash` |
| `-x`              | Ejecutar comando  | `-x "whoami"`          |
| `--port <PUERTO>` | Determinar puerto |                        |

# General 
| **Flag**                    | **Función**           | **Ejemplo**                                              |
| ----------------------- | ----------------- | ---------------------------------------------------- |
| `-d`                    | Dominio           | `-d LAB.local`                                       |
| `--spray`               | Password spraying | `cme smb 10.10.10.0/24 -u users.txt -p "Winter2025"` |
| `--no-bruteforce`       | Solo spraying     | `--no-bruteforce`                                    |
| `--continue-on-success` | Seguir tras éxito | `--continue-on-success`                              |