#reference #Ciberseguridad

**Named Pipe Client Impersonation** = técnica de escalada en Windows: un servidor de **named pipe** puede **suplantar (impersonate)** el token de seguridad de un cliente que se conecta. Si conseguimos que un cliente **privilegiado (SYSTEM)** se conecte a nuestro pipe, robamos su token.

# Base

`ImpersonateNamedPipeClient()` permite al servidor del pipe asumir el contexto de seguridad del cliente. Requiere el privilegio **`SeImpersonatePrivilege`** (lo tienen cuentas de servicio: IIS, MSSQL, etc.).

```
1. Crear named pipe servidor:  \\.\pipe\miPipe
2. Forzar a un servicio SYSTEM a conectarse al pipe
3. ImpersonateNamedPipeClient() -> token de SYSTEM
4. Duplicar el token -> CreateProcessWithToken -> shell SYSTEM
```

# Familia "Potato"

Estas técnicas convierten `SeImpersonatePrivilege` en SYSTEM forzando la conexión del cliente privilegiado:

| Exploit | Mecanismo de coerción |
|---------|----------------------|
| **JuicyPotato** | DCOM/NTLM reflection (Windows ≤ Server 2016). |
| **RoguePotato** | Redirección OXID remota. |
| **PrintSpoofer** | Abusa del servicio Print Spooler vía named pipe. |
| **GodPotato / EfsPotato** | DCOM / MS-EFSR coercion (versiones modernas). |

```powershell
whoami /priv      # comprobar SeImpersonatePrivilege = Enabled
PrintSpoofer.exe -i -c cmd       # -> shell SYSTEM
GodPotato.exe -cmd "cmd /c whoami"
```

# Detección / mitigación

- Vigilar creación de named pipes anómalos + impersonación.
- Limitar `SeImpersonatePrivilege` a cuentas estrictamente necesarias.
- Parches (muchos Potato dependen de servicios/CVE concretos).

# Recursos
### [[Leaked Handlers]] · [[PowerShell_CS]] · [[LinPE_cheatsheet]]
### [HackTricks — SeImpersonate / Potato](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/roguepotato-and-printspoofer)
