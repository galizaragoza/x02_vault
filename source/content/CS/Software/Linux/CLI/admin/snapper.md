`snapper` es una herramienta de gestión de instantáneas (snapshots) para sistemas de archivos Btrfs y volúmenes LVM. Su función principal es permitir al usuario crear, comparar y revertir estados del sistema, lo que resulta vital para recuperarse de actualizaciones fallidas o errores de configuración.

|**Parámetro / Comando**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`create`|Crea una nueva instantánea manualmente.|`snapper create --description "Backup manual"`|
|`list`|Muestra todas las instantáneas existentes para una configuración.|`snapper list`|
|`delete`|Elimina una o varias instantáneas por su número de ID.|`snapper delete 10`|
|`diff`|Compara las diferencias de archivos entre dos instantáneas.|`snapper diff 1..2`|
|`undochange`|Revierte los cambios de archivos entre dos estados.|`snapper undochange 5..0`|
|`create-config`|Crea una nueva configuración para un subvolumen específico.|`snapper -c datos create-config /mnt/datos`|
|`-c`|Especifica la configuración a utilizar (por defecto es `root`).|`snapper -c home list`|
