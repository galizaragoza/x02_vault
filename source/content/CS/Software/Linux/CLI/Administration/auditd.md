`auditd` es el componente de usuario del sistema de auditoría de Linux. Su función principal es escribir los registros de auditoría en el disco. Es el corazón del sistema de seguimiento de eventos de seguridad del kernel.

|**Parámetro**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`-f`|Ejecuta el demonio en primer plano (útil para depuración).|`auditd -f`|
|`-l`|Permite el uso de enlaces simbólicos para los archivos de log.|`auditd -l`|
|`-n`|No bifurca el proceso (no se convierte en demonio de fondo).|`auditd -n`|
|`-s`|Especifica el estado de inicio (disable, enable, nochange).|`auditd -s enable`|
|`-c`|Indica un directorio alternativo para los archivos de configuración.|`auditd -c /etc/audit_custom/`|