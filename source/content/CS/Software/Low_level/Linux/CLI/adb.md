| Comando/Parámetro       | Función                                         | Ejemplo de Sintaxis                 |
| ----------------------- | ----------------------------------------------- | ----------------------------------- |
| `adb devices`           | Lista todos los dispositivos Android conectados | `adb devices -l`                    |
| `adb connect`           | Conecta a un dispositivo remoto (TCP/IP)        | `adb connect 192.168.1.100:5555`    |
| `adb disconnect`        | Desconecta un dispositivo remoto                | `adb disconnect 192.168.1.100:5555` |
| `adb kill-server`       | Termina el servidor ADB                         | `adb kill-server`                   |
| `adb start-server`      | Inicia el servidor ADB                          | `adb start-server`                  |
| `adb reboot`            | Reinicia el dispositivo                         | `adb reboot`                        |
| `adb reboot bootloader` | Reinicia en modo bootloader                     | `adb reboot bootloader`             |
| `adb reboot recovery`   | Reinicia en modo recovery                       | `adb reboot recovery`               |

# Instalación de Aplicaciones

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`adb install`|Instala una aplicación APK|`adb install app.apk`|
|`-r`|Reinstala manteniendo datos|`adb install -r app.apk`|
|`-d`|Permite downgrade de versión|`adb install -d app.apk`|
|`-g`|Concede todos los permisos|`adb install -g app.apk`|
|`adb uninstall`|Desinstala una aplicación|`adb uninstall com.paquete.app`|
|`-k`|Desinstala manteniendo datos|`adb uninstall -k com.paquete.app`|
|`adb shell pm list packages`|Lista paquetes instalados|`adb shell pm list packages -f`|

# Transferencia de Archivos

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`adb push`|Copia archivos de PC a dispositivo|`adb push archivo.txt /sdcard/`|
|`adb pull`|Copia archivos de dispositivo a PC|`adb pull /sdcard/archivo.txt .`|
|`-a`|Conserva timestamp del archivo|`adb pull -a /sdcard/foto.jpg`|

# Shell y Comandos del Sistema

| Comando/Parámetro        | Función                                | Ejemplo de Sintaxis                                    |
| ------------------------ | -------------------------------------- | ------------------------------------------------------ |
| `adb shell`              | Abre shell interactivo del dispositivo | `adb shell`                                            |
| `adb shell <comando>`    | Ejecuta comando específico             | `adb shell ls /sdcard`                                 |
| `adb shell screencap`    | Captura pantalla                       | `adb shell screencap /sdcard/pantalla.png`             |
| `adb shell screenrecord` | Graba pantalla                         | `adb shell screenrecord /sdcard/video.mp4`             |
| `adb shell input`        | Simula entrada de usuario              | `adb shell input tap 500 500`                          |
| `adb shell am`           | Actividad Manager                      | `adb shell am start -n com.android.settings/.Settings` |
| `adb shell pm`           | Package Manager                        | `adb shell pm clear com.paquete.app`                   |
|                          |                                        |                                                        |

# Depuración y Logs

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`adb logcat`|Muestra logs del sistema|`adb logcat`|
|`-c`|Limpia logs|`adb logcat -c`|
|`-s`|Filtra por etiqueta|`adb logcat -s ActivityManager`|
|`-v`|Formato de salida|`adb logcat -v time`|
|`adb bugreport`|Genera reporte de errores|`adb bugreport`|
|`adb dumpsys`|Volcado de información del sistema|`adb dumpsys battery`|

# Copias de Seguridad

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`adb backup`|Crea backup de aplicaciones|`adb backup -apk -shared -all -system`|
|`adb restore`|Restaura backup|`adb restore backup.ab`|
|`-f`|Especifica archivo de salida|`adb backup -f backup.ab -all`|

# Red y Conexión

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`adb tcpip`|Cambia a modo TCP en puerto específico|`adb tcpip 5555`|
|`adb usb`|Vuelve a modo USB|`adb usb`|
|`adb forward`|Reenvía puertos|`adb forward tcp:6100 tcp:7100`|
|`adb reverse`|Reenvía puertos inverso|`adb reverse tcp:8080 tcp:8080`|

# Información del Dispositivo

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`adb shell getprop`|Obtiene propiedades del sistema|`adb shell getprop ro.build.version`|
|`adb shell settings`|Accede a settings del sistema|`adb shell settings list system`|
|`adb shell wm size`|Muestra resolución de pantalla|`adb shell wm size`|
|`adb shell wm density`|Muestra densidad de pantalla|`adb shell wm density`|

# Root y Permisos

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`adb root`|Reinicia adbd con permisos root|`adb root`|
|`adb disable-verity`|Deshabilita verificación de disco|`adb disable-verity`|
|`adb remount`|Remonta /system como lectura/escritura|`adb remount`|