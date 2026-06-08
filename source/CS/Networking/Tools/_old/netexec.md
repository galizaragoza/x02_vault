
```
nxc [-u usuario] [-p pass/hashes/ticket] [opciones/módulos]
```

```
nxc smb 192.168.1.0/24 -u admin -p Pass123
```

| Parámetro     | Función                                    | Ejemplo de sintaxis                                |
| ------------- | ------------------------------------------ | -------------------------------------------------- |
| -u usuario    | Especifica usuario para auth               | `nxc smb 10.0.0.0/24 -u administrator`             |
| -p pass       | Especifica password, hash o ticket         | `nxc winrm target -u user -p :hash`                |
| -t threads    | Número de hilos concurrentes (default 100) | `nxc smb 192.168.1.0/24 -t 50`                     |
| --timeout     | Timeout por hilo en segundos               | `nxc ldap dc -u user -p pass --timeout 10`         |
| -M módulo     | Ejecuta módulo específico                  | `nxc smb target -M sam -u user -p pass`            |
| -o opciones   | Opciones para módulo (JSON-like)           | `nxc smb target -M spider_plus -o READ_ONLY=False` |
| --verbose     | Output detallado                           | `nxc rdp hosts -u user -p pass --verbose`          |
| -L            | Lista módulos disponibles para protocolo   | `nxc smb -L`                                       |
| --jitter      | Delay aleatorio entre conexiones (evasión) | `nxc winrm target -u user -p pass --jitter 5`      |
| --no-progress | Desactiva barra de progreso                | `nxc ftp 10.0.0.0/24 -u anon --no-progress`        |