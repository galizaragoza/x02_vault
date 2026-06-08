#reference #Ciberseguridad

**Havoc** = framework **C2** post-explotación moderno y open source, alternativa libre a Cobalt Strike. Team server en Go + cliente Qt (GUI), agente **Demon** en C/ASM con foco en evasión. Ver contexto general en [[C2 backdoor]].

# Componentes

| Pieza | Rol |
|-------|-----|
| **Team server** | Núcleo C2; gestiona listeners, agentes, operadores. |
| **Client (GUI)** | Interfaz multi-operador (Qt). |
| **Demon** | Agente/implante con técnicas de evasión. |

# Uso básico

```bash
git clone https://github.com/HavocFramework/Havoc && cd Havoc
make ts-build && make client-build
./havoc server --profile profiles/havoc.yaotl

# Cliente -> conectar al team server -> crear listener -> generar Demon
```

# Características de evasión

- **Sleep obfuscation** (Ekko/Foliage) — cifra el implante en memoria mientras duerme.
- **Indirect syscalls** y *return address spoofing* para evadir EDR.
- Carga de objetos **BOF** (Beacon Object Files).
- Perfiles maleables para disfrazar el tráfico.

# Operativa típica

```
listener (HTTP/HTTPS) -> generar Demon -> ejecutar en víctima
-> check-in -> tareas: shell, upload/download, pivoting, BOF
```

> Uso solo en engagements autorizados o labs propios.

# Recursos
### [[C2 backdoor]] · [[Merlin]]
### [Havoc — docs](https://havocframework.com/docs/welcome) · [repo](https://github.com/HavocFramework/Havoc)
