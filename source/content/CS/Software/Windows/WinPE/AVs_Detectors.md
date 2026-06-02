#reference #Ciberseguridad

Cómo detectan los **AV/EDR** y qué implica para evasión en post-explotación Windows. Conocer los mecanismos guía qué técnicas usar (uso solo en engagements autorizados).

# Mecanismos de detección

| Mecanismo | Qué hace |
|-----------|----------|
| **Firmas estáticas** | Hashes/patrones de bytes conocidos en disco. |
| **Heurística** | Características sospechosas sin firma exacta. |
| **AMSI** | Escanea scripts/contenido en memoria antes de ejecutar (PowerShell, .NET, VBA, JS). |
| **ETW** | Telemetría del kernel/proceso (Event Tracing for Windows). |
| **API/syscall hooking** | El EDR engancha funciones (`ntdll`) en userland para inspeccionar llamadas. |
| **Behavioral/ML** | Cadenas de comportamiento (proceso A → inyecta en B → LSASS). |
| **Reputación** | Firma digital, prevalencia, origen del binario. |

# Enumerar defensas

```powershell
# Windows Defender
Get-MpComputerStatus
Get-MpPreference | Select Exclusion*       # exclusiones (¡oro!)

# Productos AV instalados (WMI)
Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntiVirusProduct
sc query windefend
```

# Conceptos de evasión

| Técnica | Contra |
|---------|--------|
| **Ofuscación / cifrado de payload** | Firmas estáticas. |
| **AMSI bypass** | AMSI (parchear `AmsiScanBuffer`/`amsiInitFailed`). |
| **ETW patching** | Telemetría ETW. |
| **Unhooking** (restaurar `ntdll` limpia) | Hooks de userland del EDR. |
| **Direct/indirect syscalls** | Evitar las APIs hookeadas. |
| **Sleep obfuscation** (Ekko/Foliage) | Escaneo de memoria en reposo. |
| **LOLBins** | Reputación (usar binarios firmados de Windows). |
| **Exclusiones del AV** | Operar dentro de rutas excluidas. |

> Las firmas cambian: cualquier bypass es temporal. Combinar varias capas. Probar en lab con el mismo EDR antes del engagement.

# Recursos
### [[PowerShell_CS]] · [[Pipe_Client_Impers]] · [[Merlin]]
### [HackTricks — AV bypass](https://book.hacktricks.xyz/windows-hardening/av-bypass) · [Defender enumeration](https://book.hacktricks.xyz/windows-hardening/basic-cmd-for-pentesters)
