Muestra información de procesos activos en el sistema.

```
ps [opciones]
```

| **Parámetro** | **Función**                                                         | **Ejemplo de Sintaxis**     |
| ------------- | ------------------------------------------------------------------- | --------------------------- |
| aux           | Formato BSD completo (todos los usuarios)                           | `ps aux`                    |
| -ef           | Formato System V completo (todos los procesos)                      | `ps -ef`                    |
| -u usuario    | Procesos de un usuario específico                                   | `ps -u www-data`            |
| -p PID        | Procesos por PID (uno o varios)                                     | `ps -p 1234 5678`           |
| -C nombre     | Procesos por nombre exacto de comando                               | `ps -C nc`                  |
| -o formato    | Salida personalizada (pid,ppid,user,cmd,...)                        | `ps -eo pid,user,comm,args` |
| --ppid PID    | Procesos hijos de un PID                                            | `ps --ppid 1337`            |
| -L            | Muestra threads (LWP) de cada proceso                               | `ps -p 1234 -L`             |
| axw           | Máxima información + argumentos completos                           | `ps axw`                    |
| -f --forest   | Árbol jerárquico de procesos                                        | `ps -ef --forest`           |
| -e            | Muestra todos los procesos del sistema (equivalente a `aux` en BSD) | `ps -e`                     |
| -d            | Muestra todos los procesos excepto líderes de sesión                | `ps -d`                     |
| -ed           | Todos los procesos excepto líderes de sesión (daemons limpios)      | `ps -ed`                    |