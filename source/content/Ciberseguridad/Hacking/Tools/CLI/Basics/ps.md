Muestra información de procesos activos en el sistema.

```
ps [opciones]
```

| PID            | TTY                                                | STAT                                          | TIME                                                               | CMD                                      |
| -------------- | -------------------------------------------------- | --------------------------------------------- | ------------------------------------------------------------------ | ---------------------------------------- |
| ID del proceso | El dispositivo terminal en el que corre el proceso | El estado del proceso ([[ps#Process states]]) | La cantidad de tiempo que el proceso ha estado corriendo en la CPU | El comando usado para correr el programa |


| **Parámetro**       | **Función**                                                         | **Ejemplo de Sintaxis**     |
| ------------------- | ------------------------------------------------------------------- | --------------------------- |
| aux                 | Formato BSD completo (todos los usuarios)                           | `ps aux`                    |
| -ef                 | Formato System V completo (todos los procesos)                      | `ps -ef`                    |
| -u usuario          | Procesos de un usuario específico                                   | `ps -u www-data`            |
| -p PID              | Procesos por PID (uno o varios)                                     | `ps -p 1234 5678`           |
| -C nombre           | Procesos por nombre exacto de comando                               | `ps -C nc`                  |
| -o formato          | Salida personalizada (pid,ppid,user,cmd,...)                        | `ps -eo pid,user,comm,args` |
| --ppid PID          | Procesos hijos de un PID                                            | `ps --ppid 1337`            |
| -L                  | Muestra threads (LWP) de cada proceso                               | `ps -p 1234 -L`             |
| axw                 | Máxima información + argumentos completos                           | `ps axw`                    |
| -f --forest         | Árbol jerárquico de procesos                                        | `ps -ef --forest`           |
| -e                  | Muestra todos los procesos del sistema (equivalente a `aux` en BSD) | `ps -e`                     |
| -d                  | Muestra todos los procesos excepto líderes de sesión                | `ps -d`                     |
| -ed                 | Todos los procesos excepto líderes de sesión (daemons limpios)      | `ps -ed`                    |
| `-ejH`              | Printea un tree de procesos                                         | `ps -ejH`                   |
| `-U root -u root u` | Para ver todos los procesos corriendo como root                     | `ps -U root -u root u`      |

# Process states
| **State**      | **Description**                                                     |
| -------------- | ------------------------------------------------------------------- |
| D              | uninterruptible sleep (usually IO)                                  |
| I              | Idle kernel thread                                                  |
| R              | running or runnable (on run queue)                                  |
| S              | interruptible sleep (waiting for an event to complete)              |
| T              | stopped by job control signal                                       |
| t              | stopped by debugger during the tracing                              |
| W              | paging (not valid since the 2.6.xx kernel)                          |
| X              | dead (should never be seen)                                         |
| Z              | defunct ("zombie") process, terminated but not reaped by its parent |
|                |                                                                     |
| **Character**  | **Description**                                                     |
| <              | high-priority (not nice to other users)                             |
| N              | low-priority (nice to other users)                                  |
| L              | has pages locked into memory (for real-time and custom IO)          |
| s              | is a session leader                                                 |
| l              | is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)       |
| +              | is in the foreground process group                                  |
