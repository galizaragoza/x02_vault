Capsh es una herramienta de línea de comandos en Linux para manipular y explorar capacidades (capabilities) de procesos, permitiendo ejecutar comandos con privilegios específicos sin necesidad de root completo.

```
capsh [opciones] -- [comando]
```

| **Parámetro (Flag)** | **Función**                                                            | **Ejemplo de Sintaxis**                         |
| ---------------- | ------------------------------------------------------------------ | ------------------------------------------- |
| `--print`        | Imprime las capacidades actuales del proceso.                      | `capsh --print`                             |
| `--caps=`        | Establece capacidades específicas (e.g., cap_sys_admin).           | `capsh --caps=cap_sys_admin+eip -- /bin/sh` |
| `--drop=`        | Elimina capacidades específicas.                                   | `capsh --drop=cap_sys_admin -- /bin/bash`   |
| `--user=`        | Cambia al usuario especificado.                                    | `capsh --user=nobody -- /bin/ls`            |
| `--chroot=`      | Cambia el directorio raíz (chroot).                                | `capsh --chroot=/tmp -- /bin/pwd`           |
| `--uid=`         | Establece el UID del proceso.                                      | `capsh --uid=1000 -- /bin/id`               |
| `--gid=`         | Establece el GID del proceso.                                      | `capsh --gid=1000 -- /bin/id`               |
| `--keep=`        | Mantiene capacidades al cambiar UID (0 o 1).                       | `capsh --keep=1 --uid=1000 -- /bin/sh`      |
| `--secbits=`     | Establece bits de seguridad (securebits).                          | `capsh --secbits=0x1f -- /bin/sh`           |
| `--decode=`      | Decodifica un vector hexadecimal de capacidades (de /proc/status). | `capsh --decode=0000000000000003`           |