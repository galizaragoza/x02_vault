# apt

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`update`|Actualiza la lista de paquetes disponibles desde los repositorios|`sudo apt update`|
|`upgrade`|Actualiza todos los paquetes instalados a sus versiones más recientes|`sudo apt upgrade`|
|`full-upgrade`|Actualiza paquetes, manejando cambios en dependencias que puedan requerir eliminar algunos paquetes|`sudo apt full-upgrade`|
|`install`|Instala uno o más paquetes|`sudo apt install nombre-paquete`|
|`install --reinstall`|Reinstala un paquete ya instalado|`sudo apt install --reinstall nombre-paquete`|
|`remove`|Elimina uno o más paquetes (mantiene archivos de configuración)|`sudo apt remove nombre-paquete`|
|`purge`|Elimina completamente uno o más paquetes, incluyendo archivos de configuración|`sudo apt purge nombre-paquete`|
|`autoremove`|Elimina paquetes automáticamente instalados que ya no son necesarios|`sudo apt autoremove`|
|`search`|Busca paquetes que coincidan con un patrón|`apt search "patrón de búsqueda"`|
|`show`|Muestra información detallada sobre un paquete|`apt show nombre-paquete`|
|`list`|Lista paquetes según criterios específicos|`apt list --installed`|
|`edit-sources`|Edita el archivo sources.list con el editor por defecto|`sudo apt edit-sources`|
|`--help`|Muestra ayuda sobre el uso de apt|`apt --help`|
|`--version`|Muestra la versión de apt|`apt --version`|
|`-y` / `--yes`|Responde automáticamente "sí" a todas las preguntas|`sudo apt install -y nombre-paquete`|
|`--no-install-recommends`|No instala paquetes recomendados|`sudo apt install --no-install-recommends nombre-paquete`|
|`--install-suggests`|Instala también paquetes sugeridos|`sudo apt install --install-suggests nombre-paquete`|

# apt-get

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`update`|Actualiza la lista de paquetes disponibles|`sudo apt-get update`|
|`upgrade`|Actualiza paquetes instalados|`sudo apt-get upgrade`|
|`dist-upgrade`|Equivalente a `full-upgrade` de apt|`sudo apt-get dist-upgrade`|
|`install`|Instala paquetes|`sudo apt-get install nombre-paquete`|
|`remove`|Elimina paquetes|`sudo apt-get remove nombre-paquete`|
|`purge`|Elimina completamente paquetes|`sudo apt-get purge nombre-paquete`|
|`autoremove`|Elimina paquetes no necesarios|`sudo apt-get autoremove`|
|`source`|Descarga el código fuente de un paquete|`apt-get source nombre-paquete`|
|`build-dep`|Instala dependencias para compilar un paquete|`sudo apt-get build-dep nombre-paquete`|
|`check`|Verifica la consistencia de la base de datos de paquetes|`sudo apt-get check`|
|`clean`|Elimina archivos .deb descargados del cache|`sudo apt-get clean`|
|`autoclean`|Elimina archivos .deb obsoletos del cache|`sudo apt-get autoclean`|
|`-d` / `--download-only`|Solo descarga, no instala|`sudo apt-get install -d nombre-paquete`|
|`-s` / `--simulate`|Simula la operación sin realizarla|`sudo apt-get install -s nombre-paquete`|
|`-f` / `--fix-broken`|Intenta reparar dependencias rotas|`sudo apt-get install -f`|
|`-q` / `--quiet`|Modo silencioso|`sudo apt-get install -q nombre-paquete`|

# apt-cache

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`search`|Busca paquetes por nombre o descripción|`apt-cache search "término de búsqueda"`|
|`show`|Muestra información detallada del paquete|`apt-cache show nombre-paquete`|
|`showpkg`|Muestra información sobre un paquete y sus dependencias|`apt-cache showpkg nombre-paquete`|
|`depends`|Muestra dependencias de un paquete|`apt-cache depends nombre-paquete`|
|`rdepends`|Muestra paquetes que dependen del paquete especificado|`apt-cache rdepends nombre-paquete`|
|`pkgnames`|Lista todos los nombres de paquetes disponibles|`apt-cache pkgnames`|
|`policy`|Muestra prioridades de instalación y versiones disponibles|`apt-cache policy nombre-paquete`|
|`stats`|Muestra estadísticas de la caché|`apt-cache stats`|
|`dump`|Muestra toda la información de la caché|`apt-cache dump`|
|`dumpavail`|Muestra todos los paquetes disponibles|`apt-cache dumpavail`|

# apt-mark

|Comando/Parámetro|Función|Ejemplo de Sintaxis|
|---|---|---|
|`auto`|Marca un paquete como instalado automáticamente|`sudo apt-mark auto nombre-paquete`|
|`manual`|Marca un paquete como instalado manualmente|`sudo apt-mark manual nombre-paquete`|
|`hold`|Evita que un paquete sea actualizado|`sudo apt-mark hold nombre-paquete`|
|`unhold`|Permite que un paquete sea actualizado|`sudo apt-mark unhold nombre-paquete`|
|`showauto`|Muestra paquetes instalados automáticamente|`apt-mark showauto`|
|`showmanual`|Muestra paquetes instalados manualmente|`apt-mark showmanual`|
|`showhold`|Muestra paquetes en estado "hold"|`apt-mark showhold`|

# Protips
```
sudo apt update && sudo apt dist-upgrade -y && sudo apt autoremove -y
```