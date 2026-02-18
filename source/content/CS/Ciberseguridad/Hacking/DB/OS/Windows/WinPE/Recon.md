# Información del sistema
```bash
systeminfo ## 

wmic qfe get Caption,Description,HotFixID,InstalledOn #Patches

wmic os get osarchitecture || echo %PROCESSOR_ARCHITECTURE% #Get system architecture

hostname ## Nombre del equipo

ver ## Versión del OS
```

```PowerShell
[System.Environment]::OSVersion.Version #Current OS version

Get-WmiObject -query 'select * from win32_quickfixengineering' | foreach {$_.hotfixid} #List all patches

Get-Hotfix -description "Security update" #List only "Security Update" patches

```

[MS vuln DB](https://msrc.microsoft.com/update-guide/vulnerability)

## Privilegios del usuario actual
```sh
whoami /priv ## If the user has `SeImpersonate` or `SeAssignPrimaryToken`: JACKPOT
```
[JuicyPotato](https://github.com/ohpe/juicy-potato), [wu para primeros intentos](https://berserkwings.github.io/THL-writeup-espetoMalaquenio/#)

## Environment
```bash
set

dir env:
```

```powershell
Get-ChildItem Env: | ft Key,Value -AutoSize
```


## PowerShell

### PS history
```bash
ConsoleHost_history #Find the PATH where is saved1

type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
type C:\Users\swissky\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```


```powershell
cat (Get-PSReadlineOption).HistorySavePath
cat (Get-PSReadlineOption).HistorySavePath | sls passw
```

### Powershell Transcripts
```bash
#Check is enable in the registry
reg query HKCU\Software\Policies\Microsoft\Windows\PowerShell\Transcription
reg query HKLM\Software\Policies\Microsoft\Windows\PowerShell\Transcription
reg query HKCU\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\Transcription
reg query HKLM\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\Transcription
dir C:\Transcripts
```

```powershell
#Start a Transcription session
Start-Transcript -Path "C:\transcripts\transcript0.txt" -NoClobber
Stop-Transcript
```

### PowerShell Module Logging
```sh
reg query HKCU\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging
reg query HKLM\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging
reg query HKCU\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging
reg query HKLM\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging
```

```powershell
Get-WinEvent -LogName "windows Powershell" | select -First N | Out-GridView
```

### PS Script Block Logging
```bash
reg query HKCU\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging
reg query HKLM\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging
reg query HKCU\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging
reg query HKLM\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging
```

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Powershell/Operational" | select -first N | Out-Gridview 
## View the last N events
```


## Internet Settings
```bash
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
```


## Drives
```bash
wmic logicaldisk get caption || fsutil fsinfo drives
wmic logicaldisk get caption,description,providername
```

```powershell
Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root
```



# WSUS
Se puede comprometer el sistema si las actualizaciones se solicitan mediante HTTP y no HTTP**S**

```sh
reg query HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v WUServer
```

```powershell
Get-ItemProperty -Path HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate -Name "WUServer"
```

```powershell
## De obtener una respuesta como esta
HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\WindowsUpdate
      WUServer    REG_SZ    http://xxxx-updxx.corp.internal.com:8535
      
## O esta otra
WUServer     : http://xxxx-updxx.corp.internal.com:8530
PSPath       : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\software\policies\microsoft\windows\windowsupdate
PSParentPath : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\software\policies\microsoft\windows
PSChildName  : windowsupdate
PSDrive      : HKLM
PSProvider   : Microsoft.PowerShell.Core\Registry

## O si alguna de estas equivale a 1, la máquina es vulnerable
HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v UseWUServer
Get-ItemProperty -Path hklm:\software\policies\microsoft\windows\windowsupdate\au -name "usewuserver"
```
[Wsuxploit](https://github.com/pimps/wsuxploit), [pywsus](https://github.com/GoSecure/pywsus)


# 3rd Party Auto Updaters
https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/abusing-auto-updaters-and-ipc.html



# Users & Groups
```sh
net users %username% #Me
net users #All local users
net localgroup #Groups
net localgroup Administrators #Who is inside Administrators group
whoami /all #Check the privileges
```

```powershell
Get-WmiObject -Class Win32_UserAccount
Get-LocalUser | ft Name,Enabled,LastLogon
Get-ChildItem C:\Users -Force | select Name
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
```


## Privileged Groups
https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/privileged-groups-and-token-privileges.html


## Token manipulation
https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/privilege-escalation-abusing-tokens.html


## Users, folders, passwd policy
```bash
## Logged Users
qwinsta
klist sessions

## Home folders
dir C:\Users
Get-ChildItem C:\Users

## Password Policy
net accounts
```


## Clipboard content
```powershell
powershell -command "Get-Clipboard"
```



# Running Processes
```bash
Tasklist /SVC #List processes running and services
tasklist /v /fi "username eq system" #Filter "system" processes
```

```powershell
#With allowed Usernames
Get-WmiObject -Query "Select * from Win32_Process" | where {$_.Name -notlike "svchost*"} | Select Name, Handle, @{Label="Owner";Expression={$_.GetOwner().User}} | ft -AutoSize

#Without usernames
Get-Process | where {$_.ProcessName -notlike "svchost*"} | ft ProcessName, Id
```


## Perms of processes binaries
```bash
for /f "tokens=2 delims='='" %%x in ('wmic process list full^|find /i "executablepath"^|find /i /v "system32"^|find ":"') do (
    for /f eol^=^"^ delims^=^" %%z in ('echo %%x') do (
        icacls "%%z"
2>nul | findstr /i "(F) (M) (W) :\\" | findstr /i ":\\ everyone authenticated users todos %username%" && echo.
    )
)
```


## Memory password mining
Si un proceso almacena creds en plaintext, se puede hacer un procdump para analizar.
```bash
procdump.exe -accepteula -ma <proc_name_tasklist>
```


## Insecure GUI
Algunas apps corriendo como SYSTEM tienen opciones para abrir CMD heredando privilegios.



# Services
Ver una lista de servicios:
```bash
net start
wmic service list brief
sc query
```

```powershell
Get-Service
```


## Permisos
```sh
sc qc <service_name> ## Comprobar info de un servicio
accesschk.exe -ucqv <Service_Name> #Check rights for different groups

## Comprobar si "Authenticated Users" puede modificar algún servicio
accesschk.exe -uwcqv "Authenticated Users" * /accepteula
accesschk.exe -uwcqv %USERNAME% * /accepteula
accesschk.exe -uwcqv "BUILTIN\Users" * /accepteula 2>nul
accesschk.exe -uwcqv "Todos" * /accepteula ::Spanish version
```
[accesschk.exe](https://github.com/ankh2054/windows-pentest/raw/master/Privelege/accesschk-2003-xp.exe)


# Applications
Comprobar permisos de los binarios
```bash
dir /a "C:\Program Files"
dir /a "C:\Program Files (x86)"
reg query HKEY_LOCAL_MACHINE\SOFTWARE
```

```powershell
Get-ChildItem 'C:\Program Files', 'C:\Program Files (x86)' | ft Parent,Name,LastWriteTime
Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name
```


## Permisos de escritura
```bash
accesschk.exe /accepteula
# Find all weak folder permissions per drive.
accesschk.exe -uwdqs Users c:\
accesschk.exe -uwdqs "Authenticated Users" c:\
accesschk.exe -uwdqs "Everyone" c:\
# Find all weak file permissions per drive.
accesschk.exe -uwqs Users c:\*.*
accesschk.exe -uwqs "Authenticated Users" c:\*.*
accesschk.exe -uwdqs "Everyone" c:\*.*
```

```bash
icacls "C:\Program Files\*" 2>nul | findstr "(F) (M) :\" | findstr ":\ everyone authenticated users todos %username%"
icacls ":\Program Files (x86)\*" 2>nul | findstr "(F) (M) C:\" | findstr ":\ everyone authenticated users todos %username%"
```

```powershell
Get-ChildItem 'C:\Program Files\*','C:\Program Files (x86)\*' | % { try { Get-Acl $_ -EA SilentlyContinue | Where {($_.Access|select -ExpandProperty IdentityReference) -match 'Everyone'} } catch {}}

Get-ChildItem 'C:\Program Files\*','C:\Program Files (x86)\*' | % { try { Get-Acl $_ -EA SilentlyContinue | Where {($_.Access|select -ExpandProperty IdentityReference) -match 'BUILTIN\Users'} } catch {}}
```


## Drivers
Identificar 3rd party drivers raros o vulnerables
```bash
driverquery
driverquery.exe /fo table
driverquery /SI
```


# Network
## Shares
```bash
## Interfaces, Routes, Ports, Hosts and DNSCache

ipconfig /all #Info about interfaces
route print #Print available routes
arp -a #Know hosts
netstat -ano #Opened ports?
type C:\WINDOWS\System32\drivers\etc\hosts
ipconfig /displaydns | findstr "Record" | findstr "Name Host"

## Shares
net view #Get a list of computers
net view /all /domain [domainname] #Shares on the domains
net view \\computer /ALL #List shares of a computer
net use x: \\computer\share #Mount the share locally
net share #Check current shares

## hosts file
type C:\Windows\System32\drivers\etc\hosts

## Interfaces de red y DNS
ipconfig /all
Get-NetIPConfiguration | ft InterfaceAlias,InterfaceDescription,IPv4Address
Get-DnsClientServerAddress -AddressFamily IPv4 | ft

## Puertos abiertos
netstat -ano #Opened ports?

## Routing table
route print
Get-NetRoute -AddressFamily IPv4 | ft DestinationPrefix,NextHop,RouteMetric,ifIndex

## ARP table
arp -A
Get-NetNeighbor -AddressFamily IPv4 | ft ifIndex,IPAddress,L
```
### Firewall cmds [https://book.hacktricks.wiki/en/windows-hardening/basic-cmd-for-pentesters.html#firewall]




# Win Credentials



# Files and reg




# Tools
### [WinPEAS](https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS)
### [WinExploitSuggester](https://github.com/strozfriedberg/Windows-Exploit-Suggester)
### [PoC DB](https://github.com/nomi-sec/PoC-in-GitHub)
### [Windows Kernel Exploits](https://github.com/SecWiki/windows-kernel-exploits)
### [Windows Exploits](https://github.com/abatchy17/WindowsExploits)