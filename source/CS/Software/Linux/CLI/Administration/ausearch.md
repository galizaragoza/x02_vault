`ausearch` es una herramienta diseñada para consultar los registros del demonio de auditoría (`auditd`). Dado que los logs de auditoría pueden ser densos y complejos, esta utilidad permite filtrar eventos específicos de forma legible.

| **Parámetro** | **Función**                                                   | **Ejemplo de Sintaxis**   |
| ------------- | ------------------------------------------------------------- | ------------------------- |
| `-a`          | Busca por el ID de evento de auditoría específico.            | `ausearch -a 1234`        |
| `-c`          | Busca por el nombre del comando ejecutado.                    | `ausearch -c 'sudo'`      |
| `-f`          | Busca eventos relacionados con un archivo específico.         | `ausearch -f /etc/passwd` |
| `-i`          | Interpreta las entidades numéricas (convierte UID a nombres). | `ausearch -i -c 'ssh'`    |
| `-m`          | Busca por el tipo de mensaje de auditoría (ej. USER_LOGIN).   | `ausearch -m USER_LOGIN`  |
| `-ts`         | Filtra eventos desde una fecha/hora específica (Start Time).  | `ausearch -ts today`      |
| `-ua`         | Busca eventos asociados a un usuario específico (User ID).    | `ausearch -ua 1000`       |