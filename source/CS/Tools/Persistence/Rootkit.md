#reference #Ciberseguridad

**Rootkit** = malware diseñado para **mantener acceso privilegiado y ocultarse** (a sí mismo, procesos, ficheros, conexiones) del sistema y sus defensas. Opera interceptando (*hooking*) las funciones que el SO usa para listar recursos.

# Tipos por nivel

| Tipo | Dónde vive | Sigilo / dificultad |
|------|-----------|---------------------|
| **Userland** | Binarios/librerías de usuario (`LD_PRELOAD`, hooking de libc) | Bajo; detectable comparando con binarios limpios. |
| **Kernel (LKM)** | Módulo del kernel | Alto; ve y altera todo el SO. |
| **Bootkit** | Bootloader/MBR/UEFI | Persiste antes del SO. |
| **Firmware/Hardware** | BIOS/UEFI, periféricos | Máximo; sobrevive a reinstalación. |

# Técnicas de hooking (Linux)

| Técnica | Idea |
|---------|------|
| **LD_PRELOAD** | Precargar una `.so` que sustituye funciones (userland). |
| **syscall table hooking** | Reemplazar punteros de la tabla de syscalls. |
| **ftrace / kprobes hooking** | Interceptar funciones del kernel sin tocar la tabla. |
| **VFS hooking** | Ocultar ficheros/dirs filtrando `readdir`. |

Ocultan: procesos (filtrando `/proc`), ficheros, puertos (`netstat`), módulos (`lsmod`), y suelen exponer una **puerta trasera** (magic packet, señal).

# Detección

| Herramienta | Enfoque |
|-------------|---------|
| **chkrootkit / rkhunter** | Firmas y comprobaciones conocidas. |
| **Comparar vistas** | Diferencias entre userland y kernel (ej. `ls` vs syscall directa). |
| **Análisis de memoria** | Volatility sobre un dump (hooks, módulos ocultos). |
| **Integridad** | AIDE/Tripwire, verificar binarios del sistema. |
| **Secure Boot / firma de módulos** | Prevención a nivel kernel. |

> Un rootkit de kernel puede mentir a las herramientas que corren en el mismo sistema → el análisis fiable se hace **offline** (disco/memoria desde fuera).

# Recursos
### [[C2 backdoor]] · [[Binary analysis]]
### [Elastic — Linux rootkits: hooking techniques](https://www.elastic.co/security-labs/linux-rootkits-1-hooked-on-linux#rootkit-internals-and-hooking-techniques)
