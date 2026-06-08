`paru` es un **AUR Helper** (asistente para el Arch User Repository) escrito en Rust. Es el sucesor espiritual de `yay` y se utiliza para simplificar la instalación de paquetes que no están en los repositorios oficiales de Arch Linux.

|**Parámetro**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`-S`|Instala un paquete (busca en repositorios oficiales y AUR).|`paru -S google-chrome`|
|`-Sua`|Actualiza exclusivamente los paquetes instalados desde el AUR.|`paru -Sua`|
|`-Ss`|Busca una palabra clave en las descripciones de los paquetes.|`paru -Ss visual-studio-code`|
|`-Si`|Muestra información detallada de un paquete específico.|`paru -Si spotify`|
|`-Rns`|Elimina un paquete, sus configuraciones y dependencias innecesarias.|`paru -Rns discord`|
|`-G`|Descarga el PKGBUILD y archivos fuente sin instalar nada.|`paru -G zoom`|
|`-C`|Limpia los archivos temporales de compilación (caché).|`paru -C`|
