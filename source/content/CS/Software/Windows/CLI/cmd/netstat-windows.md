# netstat (Windows)

**`netstat`** en Windows muestra conexiones TCP/UDP activas, puertos en escucha, la tabla de enrutamiento, estadísticas por protocolo e interfaz y —a diferencia de la versión Linux— el proceso/ejecutable dueño de cada conexión. Es una herramienta nativa de `cmd.exe` (no requiere instalación) y usa flags con **una sola letra y prefijo `-`** (o `/`). Ojo: su sintaxis difiere de la de `net-tools` de Linux; aquí no existen `-t/-u/-l/--program`. Sintaxis base: `netstat [-abefnoqrstxy] [-p proto] [intervalo]`.

> Requiere símbolo del sistema **elevado** (Administrador) para `-b` y para ver todos los PID con `-o`.

```
netstat [-a] [-b] [-e] [-f] [-n] [-o] [-p proto] [-q] [-r] [-s] [-t] [-x] [-y] [intervalo]
```

---

## Selección de conexiones

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-a` | Muestra todas las conexiones y puertos en escucha. | `netstat -a` |
| `-q` | Todas las conexiones + puertos TCP ligados no en escucha. | `netstat -q` |
| `-p proto` | Filtra por protocolo: `TCP`, `UDP`, `TCPv6`, `UDPv6`. | `netstat -p TCP` |

---

## Identificación de proceso

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-o` | Muestra el PID dueño de cada conexión. | `netstat -ano` |
| `-b` | Muestra el ejecutable que creó la conexión (requiere Administrador). | `netstat -abno` |

---

## Formato de salida

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-n` | Direcciones y puertos en formato numérico (sin resolver DNS). | `netstat -an` |
| `-f` | Muestra el FQDN de las direcciones remotas. | `netstat -f` |
| `intervalo` | Redibuja la salida cada N segundos (Ctrl+C para parar). | `netstat -an 5` |

---

## Enrutamiento, estadísticas e interfaces

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-r` | Muestra la tabla de enrutamiento (equivale a `route print`). | `netstat -r` |
| `-e` | Estadísticas de Ethernet (bytes/paquetes). Combinable con `-s`. | `netstat -e -s` |
| `-s` | Estadísticas por protocolo (IP, ICMP, TCP, UDP y sus variantes v6). | `netstat -s` |
| `-s -p proto` | Estadísticas de un protocolo concreto. | `netstat -s -p tcp` |

---

## Opciones avanzadas (offload / NetworkDirect)

| Parámetro | Función | Ejemplo |
| --- | --- | --- |
| `-t` | Muestra el estado de descarga (offload) de la conexión actual. | `netstat -t` |
| `-x` | Muestra conexiones, listeners y endpoints compartidos NetworkDirect (RDMA). | `netstat -x` |
| `-y` | Muestra la plantilla de conexión TCP para todas o una conexión. No combinable con otras. | `netstat -y` |

---

## Casos prácticos

```bat
:: Qué proceso escucha en un puerto (PID + numérico)
netstat -ano | findstr :445

:: Conexiones con ejecutable dueño (elevado)
netstat -abno

:: Solo TCP establecidas, sin DNS
netstat -np TCP | findstr ESTABLISHED

:: Estadísticas de TCP
netstat -s -p tcp

:: Refresco continuo cada 3 s
netstat -ano 3
```

> Equivalente moderno en PowerShell: `Get-NetTCPConnection` y `Get-NetUDPEndpoint` (permiten filtrar por estado, PID y proceso sin parsear texto).

Ver también [[netstat]] (versión Linux) · [[CMD_101]] · [[whoami]].
