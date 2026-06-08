lsof (list open files) lista los ficheros abiertos por procesos del sistema, incluyendo ficheros regulares, directorios, bibliotecas, dispositivos, sockets de red, pipes y ficheros especiales. En Unix "todo es un fichero", por lo que lsof es la herramienta definitiva para entender qué recursos usa cada proceso, qué puertos están en uso, quién tiene abierto un fichero dado, y para diagnosticar procesos zombi o recursos bloqueados.

```
lsof [opciones] [nombre...]
```

> Los argumentos de nombre (ficheros, directorios, sockets) se combinan con OR por defecto. Usar `-a` para AND entre selecciones.

---

## Selección por proceso

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-p <PID>[,PID...]` | Lista ficheros abiertos por el/los PID indicados. | `lsof -p 1234` |
| `-p ^<PID>` | Excluye el PID indicado. | `lsof -p ^1` |
| `-c <nombre>` | Lista ficheros de procesos cuyo nombre de comando empieza por `nombre`. | `lsof -c nginx` |
| `-u <usuario>` | Lista ficheros de procesos del usuario (nombre o UID). | `lsof -u www-data` |
| `-u ^<usuario>` | Excluye el usuario indicado. | `lsof -u ^root` |
| `-g <PGID>` | Lista ficheros de procesos del grupo de procesos indicado. | `lsof -g 1234` |

---

## Selección por red

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` | Lista todos los sockets de red abiertos. | `lsof -i` |
| `-i <[46][proto][@host][:svc/port]>` | Filtra por versión IP, protocolo, host y/o puerto. | `lsof -i tcp:80` |
| `-i :<puerto>` | Procesos que usan el puerto indicado. | `lsof -i :22` |
| `-i tcp` / `-i udp` | Filtra por protocolo. | `lsof -i udp` |
| `-i 4` / `-i 6` | Solo IPv4 o solo IPv6. | `lsof -i 4` |
| `-i @<host>` | Conexiones al host indicado (nombre o IP). | `lsof -i @192.168.1.1` |

---

## Selección por fichero y directorio

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `<fichero>` | Procesos que tienen abierto ese fichero exacto. | `lsof /etc/passwd` |
| `+d <dir>` | Ficheros abiertos directamente en el directorio (no recursivo). | `lsof +d /var/log` |
| `+D <dir>` | Ficheros abiertos en el directorio y todos sus subdirectorios. | `lsof +D /home/usuario` |
| `-D <dir>` | Especifica directorio de caché de dispositivos. | — |

---

## Modificadores lógicos y de salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-a` | AND lógico: combina todas las selecciones en lugar de OR. | `lsof -a -u root -i tcp` |
| `-n` | No resuelve nombres de host (IPs numéricas). Más rápido. | `lsof -n -i` |
| `-P` | No resuelve números de puerto a nombres de servicio. | `lsof -P -i :80` |
| `-t` | Salida compacta: solo PIDs (sin cabecera). Útil en scripts y con `kill`. | `lsof -t -i :80` |
| `-R` | Muestra el PPID (PID del proceso padre) de cada proceso. | `lsof -R -p 1234` |
| `-l` | Muestra UID numérico en lugar del nombre de usuario. | `lsof -l -u 1000` |
| `-r <segundos>` | Repite el listado cada `segundos` segundos indefinidamente. | `lsof -r 2 -i :80` |
| `+r <segundos>` | Repite hasta que no queden ficheros seleccionados. | `lsof +r 1 /tmp/fichero` |
| `-s <proto:estado>` | Filtra sockets por estado (TCP: LISTEN, ESTABLISHED, CLOSE_WAIT, etc.). | `lsof -i tcp -s tcp:LISTEN` |
| `-F [campos]` | Salida en formato para scripts (campos separados por NUL o newline). | `lsof -F p -i :80` |
| `-V` | Muestra elementos de selección que no se encontraron. | `lsof -V /fichero/inexistente` |

---

## Casos de uso comunes

```bash
# Ver qué proceso usa el puerto 80
lsof -i :80

# Puertos en escucha (todos los procesos)
lsof -i -s tcp:LISTEN

# Solo puertos en escucha, numéricos, sin resolución DNS
lsof -nP -i tcp -s tcp:LISTEN

# Todos los sockets de red del usuario actual
lsof -u $(whoami) -i

# Ficheros abiertos por PID
lsof -p 1234

# Matar todos los procesos que usan un puerto
kill $(lsof -t -i :8080)

# Qué proceso tiene abierto un fichero
lsof /var/log/syslog

# Todos los ficheros abiertos en un directorio
lsof +D /var/www/html

# Procesos de un usuario específico con sockets TCP
lsof -a -u www-data -i tcp

# Ver conexiones establecidas de un proceso
lsof -p 1234 -a -i -s tcp:ESTABLISHED

# Monitorizar en tiempo real (cada 2 segundos)
lsof -r 2 -i :443

# Ficheros que impiden desmontar un filesystem
lsof +D /mnt/usb
```
