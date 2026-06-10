**ftp** es el cliente de línea de comandos del protocolo **File Transfer Protocol** (RFC 959). Abre una sesión interactiva contra un servidor FTP para autenticarse, navegar el árbol remoto y transferir archivos sobre un canal de control (puerto 21) más canales de datos separados (modo activo o pasivo). En Linux la implementación estándar es `netkit-ftp`; en BSD/macOS es `tnftp`, que añade flags adicionales (señalados abajo). Sigue siendo útil en pentest para enumerar y exfiltrar sobre servicios FTP heredados y mal configurados.

```
ftp [opciones] [host [puerto]]
```

## Opciones de invocación (línea de comandos)

| **Flag** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-4` | Fuerza el uso exclusivo de IPv4. | `ftp -4 ftp.target.com` |
| `-6` | Fuerza el uso exclusivo de IPv6. | `ftp -6 ftp.target.com` |
| `-p` | Activa modo pasivo desde el arranque (evita problemas con NAT/firewall). | `ftp -p 10.10.10.5` |
| `-i` | Desactiva la confirmación interactiva en transferencias múltiples (`mget`/`mput`). | `ftp -in 10.10.10.5` |
| `-n` | No intenta auto-login; ignora la entrada de `.netrc`. | `ftp -n 10.10.10.5` |
| `-e` | Deshabilita la edición de línea de comandos y el historial. | `ftp -e 10.10.10.5` |
| `-g` | Deshabilita el *globbing* de nombres de archivo (metacaracteres `*`, `?`). | `ftp -g 10.10.10.5` |
| `-v` | Modo verboso: muestra todas las respuestas del servidor y estadísticas de transferencia. | `ftp -v 10.10.10.5` |
| `-d` | Activa el modo de depuración (debug); imprime los comandos enviados. | `ftp -d 10.10.10.5` |
| `-t` | Activa el trazado de paquetes (packet tracing). | `ftp -t 10.10.10.5` |
| `-N netrc` | (tnftp) Indica un archivo `.netrc` alternativo. | `ftp -N ./creds.netrc target` |
| `-r seg` | (tnftp) Reintenta la conexión cada *seg* segundos si falla. | `ftp -r 10 10.10.10.5` |
| `-A` | (tnftp) Fuerza modo activo. | `ftp -A 10.10.10.5` |
| `-f` | (tnftp) Fuerza la recarga de la caché para HTTP/FTP por URL. | `ftp -f http://host/file` |
| `-R` | (tnftp) Reanuda (restart) una transferencia previa. | `ftp -R ftp://host/big.iso` |
| `-V` | (tnftp) Desactiva el modo verboso. | `ftp -V 10.10.10.5` |

> Sintaxis no interactiva habitual: usar `.netrc` (con `machine`/`login`/`password`) más `-n`, o canalizar comandos por *here-doc*:
> ```bash
> ftp -inv 10.10.10.5 <<EOF
> user anonymous anonymous
> binary
> get flag.txt
> bye
> EOF
> ```

## Comandos de sesión y autenticación

| **Comando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `open host [puerto]` | Establece conexión con un servidor. | `open 10.10.10.5 21` |
| `user [nombre [pass [cuenta]]]` | Autentica con un usuario en la sesión activa. | `user anonymous anonymous` |
| `account [pass]` | Envía una contraseña de cuenta adicional si el servidor la pide. | `account billing` |
| `close` / `disconnect` | Cierra la sesión actual sin salir del cliente. | `close` |
| `bye` / `quit` | Cierra la sesión y termina el cliente. | `bye` |
| `reset` | Limpia la cola de respuestas para resincronizar con el servidor. | `reset` |
| `proxy comando` | Ejecuta un comando en una segunda conexión de control (transferencia servidor-a-servidor / FXP). | `proxy get archivo` |

## Navegación de directorios

| **Comando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `cd dir` | Cambia el directorio de trabajo remoto. | `cd /pub/uploads` |
| `cdup` | Sube al directorio padre remoto. | `cdup` |
| `pwd` | Muestra el directorio de trabajo remoto. | `pwd` |
| `lcd [dir]` | Cambia el directorio de trabajo local. | `lcd /tmp/loot` |
| `ls [dir [file]]` | Lista el contenido remoto (formato corto), opcionalmente lo guarda en local. | `ls -la salida.txt` |
| `dir [dir [file]]` | Listado largo del directorio remoto. | `dir /etc` |
| `nlist [dir [file]]` | Lista solo los nombres de archivo del directorio. | `nlist` |
| `mls remoto local` | Listado de múltiples directorios a un archivo local. | `mls *.conf lista.txt` |
| `mdir remoto local` | Listado largo de múltiples directorios a un archivo local. | `mdir /var/* dir.txt` |

## Transferencia de archivos

| **Comando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `get remoto [local]` / `recv` | Descarga un archivo del servidor. | `get /etc/passwd passwd.bak` |
| `put local [remoto]` / `send` | Sube un archivo al servidor. | `put shell.php` |
| `mget patrón` | Descarga múltiples archivos según patrón. | `mget *.log` |
| `mput patrón` | Sube múltiples archivos según patrón. | `mput *.txt` |
| `append local [remoto]` | Añade un archivo local al final de uno remoto. | `append extra.txt registro.txt` |
| `reget remoto [local]` | Reanuda una descarga interrumpida (restart get). | `reget grande.iso` |
| `restart marca` | Fija el punto de reinicio (offset en bytes) para el siguiente get/put. | `restart 1048576` |
| `newer remoto [local]` | Descarga solo si el remoto es más nuevo que el local. | `newer backup.tar` |

## Modo y tipo de transferencia

| **Comando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `ascii` | Tipo de transferencia texto ASCII (convierte fin de línea). | `ascii` |
| `binary` | Tipo de transferencia binario (imagen); sin conversión. Usar siempre para ejecutables. | `binary` |
| `type [nombre]` | Fija o muestra el tipo de representación (`ascii`, `binary`, `image`, `tenex`). | `type binary` |
| `tenex` | Tipo de transferencia para máquinas TENEX (palabras de 8 bits). | `tenex` |
| `form formato` | Fija el formato de transferencia (normalmente `non-print`). | `form non-print` |
| `mode [modo]` | Fija el modo de transferencia (`stream`, etc.). | `mode stream` |
| `struct [estructura]` | Fija la estructura del archivo (`file`, `record`, `page`). | `struct file` |

## Conmutadores y ajustes de sesión

| **Comando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `passive` | Alterna el modo pasivo on/off. | `passive` |
| `prompt` | Alterna la confirmación interactiva en `mget`/`mput`/`mdelete`. | `prompt` |
| `glob` | Alterna la expansión de metacaracteres en nombres locales. | `glob` |
| `hash [tam]` | Alterna la impresión de `#` por cada bloque transferido (barra de progreso). | `hash` |
| `verbose` | Alterna el modo verboso dentro de la sesión. | `verbose` |
| `bell` | Hace sonar la campana al terminar cada transferencia. | `bell` |
| `sunique` | Alterna nombres únicos en el servidor (STOU) al subir. | `sunique` |
| `runique` | Alterna nombres únicos en local al descargar. | `runique` |
| `case` | Al `mget`, conmuta nombres remotos en mayúsculas a minúsculas en local. | `case` |
| `cr` | Conmuta el stripping de CR en transferencias ASCII. | `cr` |
| `ntrans [in [out]]` | Define una tabla de traducción de caracteres de nombres de archivo. | `ntrans /_` |
| `nmap [patrón_in patrón_out]` | Define una plantilla de mapeo de nombres de archivo. | `nmap $1.$2 $1` |
| `debug [n]` | Alterna/fija el nivel de depuración. | `debug 1` |
| `trace` | Alterna el trazado de paquetes. | `trace` |
| `status` | Muestra el estado actual de la conexión y los conmutadores. | `status` |
| `umask [valor]` | Muestra o fija la máscara de creación de archivos en el servidor. | `umask 022` |
| `macdef nombre` | Define una macro (terminada por línea en blanco). | `macdef subir` |
| `$ macro [args]` | Ejecuta una macro definida con `macdef`. | `$ subir archivo.txt` |
| `idle [seg]` | Muestra o fija el temporizador de inactividad del servidor. | `idle 120` |

## Operaciones sobre archivos remotos

| **Comando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `delete archivo` | Borra un archivo remoto. | `delete viejo.log` |
| `mdelete patrón` | Borra múltiples archivos remotos. | `mdelete *.tmp` |
| `rename de a` | Renombra un archivo remoto. | `rename a.txt b.txt` |
| `mkdir dir` | Crea un directorio remoto. | `mkdir uploads` |
| `rmdir dir` | Elimina un directorio remoto. | `rmdir basura` |
| `chmod modo archivo` | Cambia los permisos de un archivo remoto (si el servidor lo soporta). | `chmod 755 shell.sh` |
| `modtime archivo` | Muestra la hora de última modificación del archivo remoto. | `modtime data.db` |
| `size archivo` | Muestra el tamaño en bytes del archivo remoto. | `size grande.iso` |

## Comandos crudos y de información del servidor

| **Comando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `quote comando` | Envía un comando FTP crudo literalmente al servidor. | `quote STAT` |
| `site comando` | Envía un comando específico del servidor (`SITE`). | `site exec ls` |
| `system` | Pregunta el tipo de sistema operativo del servidor (`SYST`). | `system` |
| `rstatus [archivo]` | Pide el estado remoto (`STAT`) del servidor o de un archivo. | `rstatus` |
| `rhelp [comando]` | Solicita la ayuda del servidor (`HELP`). | `rhelp` |
| `remotehelp [comando]` | Sinónimo de `rhelp`. | `remotehelp STOR` |

## Comandos locales y de ayuda

| **Comando** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `! [comando]` | Ejecuta un comando en la shell local (o abre subshell sin args). | `!ls -la` |
| `help [comando]` / `?` | Muestra la ayuda local del cliente. | `help mget` |

Relacionado: [[FTP]] (protocolo), [[telnet]], [[scp]], [[wget]], [[curl]].
