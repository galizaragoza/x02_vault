#reference #Ciberseguridad

**Leaked handle abuse** = escalada de privilegios aprovechando que un proceso **privilegiado** mantiene abierto un **handle** (a un proceso, hilo o token) que es **heredable o accesible** por un proceso de menor privilegio. Robando/duplicando ese handle se obtienen los permisos asociados.

# Idea

Un handle es una referencia a un objeto del kernel con ciertos derechos de acceso. Si un proceso SYSTEM abre un handle con `bInheritHandle=TRUE` (o con una DACL débil) y un proceso hijo/menor puede accederlo:

```
Proceso SYSTEM  --handle heredable--> objeto (proc/thread/token)
        ^ proceso de bajo privilegio duplica el handle -> hereda sus derechos
```

# Vectores

| Handle filtrado a | Abuso |
|-------------------|-------|
| **Proceso** (PROCESS_ALL_ACCESS) | Inyectar código / leer memoria del proceso SYSTEM. |
| **Hilo** (THREAD_ALL_ACCESS) | Hijack del hilo → ejecutar shellcode en su contexto. |
| **Token** (con TOKEN_DUPLICATE) | Duplicar el token SYSTEM → `CreateProcessWithToken`. |

# Detección y explotación

```
# Sysinternals handle.exe / Process Hacker: listar handles
handle.exe -a                      # todos los handles del sistema
# Buscar handles privilegiados heredables accesibles desde nuestro proceso
```

Flujo: enumerar handles → identificar uno a proceso/token SYSTEM con derechos suficientes → `DuplicateHandle` → usar (inyección o impersonación de token).

# Mitigación

No marcar handles como heredables salvo necesidad, DACLs restrictivas en objetos, mínimo privilegio en los procesos servicio, cerrar handles a tiempo.

# Recursos
### [[Pipe_Client_Impers]] · [[AVs_Detectors]]
### [HackTricks — Leaked Handle Exploitation](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/leaked-handle-exploitation)
