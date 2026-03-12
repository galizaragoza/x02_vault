`pgrep` es una utilidad de línea de comandos que busca procesos en ejecución basándose en atributos (como el nombre, el usuario o el grupo) y devuelve los **IDs de proceso (PID)** que coinciden con los criterios. Es la alternativa eficiente a la clásica tubería `ps aux | grep`.

| **Parámetro** | **Función**                                                               | **Ejemplo de sintaxis**       |
| ------------- | ------------------------------------------------------------------------- | ----------------------------- |
| `-l`          | Muestra el nombre del proceso junto con el PID.                           | `pgrep -l ssh`                |
| `-f`          | Busca el patrón en la línea de comandos completa (no solo el nombre).     | `pgrep -f "python script.py"` |
| `-u`          | Filtra procesos pertenecientes a un usuario específico (EUID).            | `pgrep -u root`               |
| `-U`          | Filtra procesos por el ID de usuario real.                                | `pgrep -U 1000`               |
| `-x`          | Busca una coincidencia exacta con el nombre del proceso.                  | `pgrep -x chrome`             |
| `-n`          | Selecciona solo el proceso coincidente más reciente (el último iniciado). | `pgrep -n bash`               |
| `-o`          | Selecciona solo el proceso coincidente más antiguo.                       | `pgrep -o nginx`              |
| `-v`          | Invierte la búsqueda (muestra procesos que NO coinciden).                 | `pgrep -v "systemd"`          |
| `-c`          | Devuelve el conteo de procesos que coinciden, en lugar de los PIDs.       | `pgrep -c apache2`            |
| `-i`          | Realiza una búsqueda que no distingue entre mayúsculas y minúsculas.      | `pgrep -i MYAPP`              |
| `-a`          | Muestra la línea de comandos completa de los procesos encontrados.        | `pgrep -a node`               |
| `-P`          | Busca procesos cuyo proceso padre (PPID) coincida con el indicado.        | `pgrep -P 1`                  |