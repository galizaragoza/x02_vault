#reference #Ciberseguridad

**Merlin** = framework **C2** post-explotación escrito en **Go**, multiplataforma, que destaca por usar **HTTP/2 y HTTP/3 (QUIC)** para el canal de comando y control. Open source. Ver contexto general en [[C2 backdoor]].

# Setup

```bash
add-apt-repository ppa:longsleep/golang-backports
apt update && sudo apt install golang-go
go version

git clone https://github.com/Ne0nd0g/merlin && cd merlin
```

# Componentes

| Pieza | Rol |
|-------|-----|
| **Server** | Team server que gestiona agentes y listeners. |
| **Agent** | Implante que corre en la víctima y hace check-in. |
| **Listener** | Endpoint (HTTP/1.1, HTTP/2, HTTP/3). |

```bash
# Servidor
go run cmd/merlinServer/main.go

# Compilar agente (cross-platform)
make agent-windows
make agent-linux
```

# Evasión (OpSec)

> "If we compile a standard agent out of the box, it will be immediately busted by any regular antivirus... we need to make some adjustments. We'll rename suspicious functions like ExecuteShell and remove references to the original package name."

Ajustes típicos antes de desplegar:
- Renombrar funciones conspicuas (`ExecuteShell`, etc.).
- Eliminar referencias al nombre del paquete original.
- Ofuscar strings, ajustar `sleep`/`jitter`.
- Firmar/empaquetar el binario.

> Uso solo en engagements autorizados o labs propios.

# Recursos
### [[C2 backdoor]] · [[Havoc]]
### [Merlin — repo](https://github.com/Ne0nd0g/merlin/)
