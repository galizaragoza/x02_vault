#reference #Ciberseguridad

**Análisis de binarios** = examinar un ejecutable sin (o con) ejecutarlo para entender su funcionamiento: malware, CTF de reversing, búsqueda de vulnerabilidades. Dos enfoques: **estático** (sin ejecutar) y **dinámico** (en ejecución).

# Triaje inicial

```bash
file binario               # tipo, arquitectura, stripped?
checksec --file=binario    # NX, PIE, canary, RELRO
strings -n 8 binario       # cadenas legibles (URLs, claves, mensajes)
nm binario                 # símbolos (si no está stripped)
ldd binario                # librerías enlazadas
xxd binario | head         # cabecera / magic bytes
```

# Análisis estático

| Herramienta | Uso |
|-------------|-----|
| **Ghidra** | Decompilador + desensamblador (NSA, gratis). |
| **IDA Pro / Free** | Estándar de la industria. |
| **radare2 / Cutter** | CLI/GUI potente y libre. |
| **Binary Ninja** | Decompilador comercial moderno. |
| `objdump -d` | Desensamblado rápido. |
| `readelf -a` | Estructura ELF (cabeceras, secciones, símbolos). |

```bash
objdump -d -M intel binario | less
r2 -A binario      # aaa; afl (listar funciones); pdf @main
```

# Análisis dinámico

| Herramienta | Uso |
|-------------|-----|
| **gdb + pwndbg/GEF** | Debugger interactivo (Linux). |
| **x64dbg** | Debugger Windows. |
| `ltrace` / `strace` | Trazar llamadas a librería / syscalls. |
| **DBI** (DynamoRIO, Frida, Pin) | Instrumentación en tiempo de ejecución. |
| **sandbox** (Cuckoo, any.run) | Detonar malware aislado. |

```bash
strace -f ./binario        # syscalls
ltrace ./binario           # llamadas a libc
gdb ./binario              # break *main; run; info registers
```

> Para malware: trabajar siempre en VM aislada/snapshot, sin red o con red simulada (INetSim).

# Conceptos clave

- **Stripped**: sin símbolos → más difícil; apóyate en strings y cross-references.
- **Packed** (UPX, etc.): el código real se desempaqueta en runtime → unpack primero (`upx -d`, dump de memoria).
- **DBI** (Dynamic Binary Instrumentation): inyectar análisis en cada instrucción sin recompilar — útil para tracing/coverage.

# Recursos
### [[Buffer Overflow]] · [[ASM_cheatsheet]] · [[RE_sideckick]]
### [Ghidra](https://ghidra-sre.org/) · [radare2](https://rada.re/) · [DBI con DynamoRIO (Talos)](https://blog.talosintelligence.com/dynamic-binary-instrumentation-dbi-with-dynamorio/)
