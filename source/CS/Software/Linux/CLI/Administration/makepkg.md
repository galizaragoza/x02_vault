## makepkg

`makepkg` es el script automatizado que se utiliza en sistemas Arch Linux para compilar y empaquetar software a partir de un archivo de instrucciones llamado `PKGBUILD`. La herramienta se encarga de descargar las fuentes, verificar sumas de control, resolver dependencias mediante `pacman` y generar un archivo comprimido `.pkg.tar.zst` instalable.

### Comportamiento de Construcción
Flags que alteran cómo se procesa el paquete y qué etapas se ejecutan.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-s`, `--syncdeps`|Instala automáticamente las dependencias faltantes usando pacman.|`makepkg -s`|
|`-i`, `--install`|Instala el paquete resultante tras una compilación exitosa.|`makepkg -si`|
|`-f`, `--force`|Fuerza la construcción del paquete incluso si ya existe uno previo.|`makepkg -f`|
|`-R`, `--repackage`|Ejecuta solo la etapa de empaquetado sin volver a compilar.|`makepkg -R`|
|`-e`, `--noextract`|No extrae las fuentes; utiliza el directorio `src/` existente.|`makepkg -e`|
|`-C`, `--cleanbuild`|Elimina el directorio `src/` antes de comenzar la extracción.|`makepkg -C`|


### Gestión de Verificación y Dependencias
Controla la integridad de los datos y los requisitos del sistema.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-g`, `--geninteg`|Genera las sumas de control (MD5/SHA) para el PKGBUILD.|`makepkg -g >> PKGBUILD`|
|`--skipinteg`|Omite la verificación de las sumas de control de los archivos fuente.|`makepkg --skipinteg`|
|`--skippgpcheck`|No verifica las firmas PGP de los archivos de origen.|`makepkg --skippgpcheck`|
|`-d`, `--nodeps`|Ignora la comprobación de dependencias del sistema.|`makepkg -d`|


### Limpieza y Miscelánea
Opciones para el mantenimiento del entorno de trabajo post-construcción.

|Opción|Descripción|Ejemplo|
|---|---|---|
|`-c`, `--clean`|Limpia los directorios de trabajo (src y pkg) tras finalizar.|`makepkg -c`|
|`-r`, `--rmdeps`|Elimina las dependencias de construcción una vez finalizado el proceso.|`makepkg -r`|
|`--log`|Crea un log detallado del proceso de compilación.|`makepkg --log`|
|`--source`|Crea un paquete solo con las fuentes (para repositorios ABS).|`makepkg --source`|
