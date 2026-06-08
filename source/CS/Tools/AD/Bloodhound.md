#reference #Ciberseguridad

**BloodHound** = herramienta que usa **teoría de grafos** para revelar relaciones ocultas y rutas de escalada de privilegios en **Active Directory** y Entra ID. Tanto blue como red team la usan para entender "quién puede llegar a Domain Admin y por dónde".

> Modela usuarios, grupos, equipos, ACLs y sesiones como nodos/aristas; luego consulta el camino más corto hasta objetivos privilegiados.

# Flujo

```
1. Recolectar datos del dominio  -> SharpHound (collector)
2. Importar el .zip/.json en BloodHound
3. Consultas / pathfinding sobre el grafo
```

# Recolección (SharpHound)

```powershell
# Ejecutable C#
SharpHound.exe -c All
SharpHound.exe -c All --zipfilename loot.zip

# Versión Python (desde Linux)
bloodhound-python -u user -p 'pass' -d corp.local -ns 10.0.0.1 -c All
```

Métodos (`-c`): `Group`, `LocalAdmin`, `Session`, `ACL`, `Trusts`, `All`, `DCOnly` (sigiloso, solo consulta el DC).

# Análisis típico

| Consulta | Para qué |
|----------|----------|
| **Shortest Path to Domain Admins** | Ruta de escalada más corta. |
| Find Principals with DCSync Rights | Quién puede [[DCsync]]. |
| Kerberoastable users | Cuentas con SPN atacables. |
| AS-REP Roastable | Cuentas sin preauth. |
| Find Computers where user is Local Admin | Movimiento lateral. |
| Owned → marcar nodos comprometidos | Recalcular rutas desde lo que ya controlas. |

> Aristas clave: `MemberOf`, `AdminTo`, `HasSession`, `GenericAll`, `WriteDacl`, `ForceChangePassword`, `AddMember`. Las ACL mal puestas (`GenericAll`/`WriteDacl`) son rutas frecuentes.

# Variantes

- **BloodHound CE** (Community Edition, web/Docker) — versión actual mantenida.
- **BloodHound Legacy** (Electron + Neo4j).

# Recursos
### [[DCsync]] · [[PowerShell_CS]] · [[PowerView]]
### [BloodHound docs](https://bloodhound.specterops.io/get-started/introduction) · [SharpHound](https://github.com/SpecterOps/SharpHound)
