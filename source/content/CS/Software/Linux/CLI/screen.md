`screen` (GNU Screen) es un **multiplexor de terminal**: permite ejecutar varias sesiones de shell dentro de un único terminal físico, separarlas del terminal (*detach*) para que sigan corriendo en segundo plano tras cerrar la conexión, y volver a unirse a ellas (*reattach*) desde otra terminal o sesión SSH. Cada sesión de `screen` contiene una o más **ventanas** (procesos independientes, típicamente shells) numeradas desde `0`, y cada ventana puede subdividirse visualmente en **regiones** (*splits*). Esta guía cubre la invocación desde línea de comandos, los atajos de teclado por defecto y la configuración vía `.screenrc`.

Casi toda la interacción dentro de una sesión se hace mediante una **tecla de prefijo** (*command character*), que por defecto es `Ctrl-a` (notado aquí como `C-a`). Se pulsa el prefijo, se suelta, y luego se pulsa la tecla de la acción. Para enviar un `Ctrl-a` literal a la aplicación de la ventana se usa `C-a a`.

> Nota de versión: los atajos de esta guía corresponden a las asignaciones por defecto de GNU Screen 4.x. El *split* vertical (`C-a |`) requiere una versión con esa función (screen ≥ 4.1; algunas distribuciones lo retroportaron a 4.0.3). Como `screen` no está instalado en este equipo, verifica siempre con `C-a ?` (ayuda de bindings) y `man screen` en el sistema destino.

---

# Invocación desde línea de comandos
Opciones al arrancar `screen` o al operar sobre sesiones existentes desde fuera.

| **Opción**            | **Descripción**                                                                                  | **Ejemplo**                        |
| --------------------- | ------------------------------------------------------------------------------------------------ | ---------------------------------- |
| `screen`              | Inicia una sesión nueva con una ventana (shell) y entra en ella.                                 | `screen`                           |
| `-S nombre`           | Crea una sesión con nombre (facilita el *reattach* posterior).                                   | `screen -S trabajo`                |
| `-t titulo`           | Fija el título de la primera ventana.                                                            | `screen -t logs`                   |
| `-ls`, `-list`        | Lista las sesiones existentes (`Attached`/`Detached`) con su `pid.tty.host`.                     | `screen -ls`                       |
| `-r [sesión]`         | Reengancha (*reattach*) a una sesión separada. Requiere identificador si hay varias.             | `screen -r trabajo`                |
| `-d -r`, `-dr`        | Separa la sesión de donde esté enganchada y la reengancha aquí.                                  | `screen -dr trabajo`               |
| `-D -r`, `-DR`        | *Power detach* + reattach; crea sesión si no existe. Útil tras caída de SSH.                     | `screen -DR trabajo`               |
| `-x [sesión]`         | *Multi-display*: se engancha a una sesión ya activa sin separarla (pantalla compartida).         | `screen -x trabajo`                |
| `-d -m`, `-dm`        | Arranca una sesión ya separada (modo demonio); no entra en ella.                                 | `screen -dmS srv ./run.sh`         |
| `-m`                  | Fuerza sesión nueva ignorando la variable `$STY` (no reengancha).                                | `screen -m`                        |
| `-p n\|nombre`        | Preselecciona una ventana al enganchar o al usar `-X`.                                           | `screen -p 2 -r trabajo`           |
| `-X comando`          | Envía un comando de `screen` a una sesión en marcha (control remoto).                            | `screen -S srv -X stuff 'ls\n'`    |
| `-L`                  | Activa el volcado a fichero de log de la ventana (`screenlog.0`).                                | `screen -L -S cap`                 |
| `-c fichero`          | Usa un fichero de configuración alternativo en vez de `~/.screenrc`.                             | `screen -c ./ci.screenrc`          |
| `-s shell`            | Shell a ejecutar en las ventanas nuevas.                                                         | `screen -s /bin/zsh`               |
| `-U`                  | Fuerza modo UTF-8.                                                                               | `screen -U`                        |
| `-h n`                | Fija el tamaño del *scrollback* (líneas de historial) por ventana.                              | `screen -h 10000`                  |
| `-e xy`               | Redefine el prefijo (`x`) y el carácter de "literal" (`y`).                                      | `screen -e ^Bb`                    |
| `-v`                  | Muestra la versión.                                                                             | `screen -v`                        |
| `-wipe`               | Elimina de la lista las sesiones muertas (`Dead`).                                               | `screen -wipe`                     |

> El identificador de sesión en `-r`/`-x` puede ser el nombre (`-S`), el `pid`, o el sufijo `pid.tty.host` que muestra `-ls`. Basta con un prefijo inequívoco.

---

# Gestión de ventanas
Una **ventana** es un proceso (shell) dentro de la sesión. Estos atajos crean, cierran y renombran ventanas. Todos van precedidos del prefijo `C-a`.

| **Atajo**       | **Comando interno** | **Acción**                                                                 |
| --------------- | ------------------- | -------------------------------------------------------------------------- |
| `C-a c`         | `screen`            | **Crea** una ventana nueva (shell) y salta a ella.                         |
| `C-a k`         | `kill`              | **Cierra** (mata) la ventana actual; pide confirmación.                    |
| `C-a A`         | `title`             | **Renombra** el título de la ventana actual.                              |
| `C-a :`         | `colon`             | Abre la línea de comandos de `screen` (equivale a `screen -X`).            |
| `C-a i`         | `info`              | Muestra información de la ventana (tamaño, flags, modo).                    |

También se cierra una ventana terminando su proceso: `exit` o `Ctrl-d` en el shell. Cuando se cierra la última ventana, la sesión de `screen` termina.

---

# Cambio entre ventanas
Navegación entre las ventanas de la sesión actual.

| **Atajo**            | **Comando interno** | **Acción**                                                              |
| -------------------- | ------------------- | ----------------------------------------------------------------------- |
| `C-a n` / `C-a Space`| `next`              | Salta a la ventana **siguiente**.                                       |
| `C-a p` / `C-a Backspace` | `prev`         | Salta a la ventana **anterior**.                                        |
| `C-a 0` … `C-a 9`    | `select 0..9`       | Salta a la ventana con ese **número**.                                  |
| `C-a '`              | `select`            | Pide número o nombre de ventana y salta a ella.                         |
| `C-a "`              | `windowlist -b`     | Muestra una **lista interactiva** de ventanas para elegir.             |
| `C-a C-a`            | `other`             | Alterna entre la ventana actual y la **última** visitada.              |
| `C-a w`              | `windows`           | Lista las ventanas en la línea de mensajes (no interactiva).           |
| `C-a N`              | `number`            | Muestra el número de la ventana actual (o lo cambia si se da argumento).|

---

# Detach / Attach (segundo plano)
El *detach* es la función central de `screen`: desliga la sesión del terminal dejando sus procesos corriendo. El *reattach* se hace normalmente desde la línea de comandos (sección de invocación).

| **Atajo / Comando**  | **Comando interno** | **Acción**                                                              |
| -------------------- | ------------------- | ----------------------------------------------------------------------- |
| `C-a d`              | `detach`            | **Separa** la sesión y vuelve al shell; los procesos siguen vivos.     |
| `C-a D D`            | `pow_detach`        | *Power detach*: separa y además cierra la sesión de login del terminal. |
| `screen -r`          | —                   | Reengancha desde otra terminal (ver sección de invocación).            |
| `screen -x`          | —                   | Se engancha en paralelo (misma vista en dos terminales).               |

> Flujo típico en SSH: `screen -S deploy` → lanzar el proceso largo → `C-a d` para separar → cerrar SSH sin matar el proceso → reconectar más tarde con `screen -r deploy`.

---

# Regiones y splits (divisiones de pantalla)
Una **región** es una porción visible de la pantalla; varias regiones muestran ventanas distintas a la vez. Dividir la pantalla **no** crea ventanas nuevas: una región recién abierta aparece vacía hasta que se selecciona o crea una ventana en ella.

| **Atajo**   | **Comando interno** | **Acción**                                                                     |
| ----------- | ------------------- | ------------------------------------------------------------------------------ |
| `C-a S`     | `split`             | Divide la región actual en **horizontal** (una encima de otra).               |
| `C-a \|`    | `split -v`          | Divide en **vertical** (lado a lado). Requiere soporte de la versión.         |
| `C-a Tab`   | `focus`             | Mueve el **foco** de entrada a la siguiente región.                            |
| `C-a X`     | `remove`            | **Cierra** la región actual (la ventana sigue existiendo en la sesión).        |
| `C-a Q`     | `only`              | Cierra **todas** las regiones menos la actual.                                 |
| `C-a :resize` | `resize`          | Redimensiona la región (`resize +5`, `resize -3`, `resize =`).                |
| `C-a F`     | `fit`               | Ajusta el tamaño de la ventana al de la región.                               |

> Patrón habitual: `C-a S` para dividir → `C-a Tab` para bajar a la región vacía → `C-a c` para crear una ventana en ella (o `C-a "` para asignar una existente).

---

# Copia y scrollback (modo copia)
El modo copia sirve tanto para **desplazarse por el historial** (*scrollback*) como para **seleccionar y copiar** texto dentro de `screen`. La navegación es de estilo `vi`.

| **Atajo**   | **Comando interno** | **Acción**                                                                     |
| ----------- | ------------------- | ------------------------------------------------------------------------------ |
| `C-a [` / `C-a Esc` | `copy`      | Entra en **modo copia/scrollback**.                                            |
| `C-a ]`     | `paste .`           | **Pega** el contenido del búfer de copia en la ventana actual.                |
| `C-a >`     | `writebuf`          | Vuelca el búfer de copia a un fichero (`/tmp/screen-exchange`).                |
| `C-a <`     | `readbuf`           | Carga ese fichero al búfer de copia.                                           |
| `C-a =`     | `removebuf`         | Elimina el fichero de intercambio.                                            |

**Dentro del modo copia:**

| **Tecla**        | **Acción**                                                        |
| ---------------- | ----------------------------------------------------------------- |
| `h j k l`, flechas | Mover el cursor.                                                 |
| `C-b` / `C-f`    | Página arriba / abajo.                                            |
| `C-u` / `C-d`    | Media página arriba / abajo.                                      |
| `0` / `$`        | Inicio / fin de línea.                                            |
| `g` / `G`        | Principio / final del búfer de scrollback.                        |
| `/` / `?`        | Buscar hacia adelante / atrás.                                    |
| `Space` (1ª vez) | **Marca el inicio** de la selección.                             |
| `Space` (2ª vez) | **Marca el fin**, copia al búfer y sale del modo copia.          |
| `Esc`            | Sale del modo copia sin copiar.                                   |

---

# Logging, monitorización y utilidades
Registro de salida, avisos de actividad y varias utilidades de la sesión.

| **Atajo**   | **Comando interno** | **Acción**                                                                     |
| ----------- | ------------------- | ------------------------------------------------------------------------------ |
| `C-a H`     | `log`               | Activa/desactiva el **log** de la ventana a `screenlog.n`.                     |
| `C-a h`     | `hardcopy`          | Vuelca la pantalla actual a un fichero (`hardcopy.n`).                         |
| `C-a M`     | `monitor`           | Vigila **actividad** en la ventana; avisa cuando produzca salida.            |
| `C-a _`     | `silence`           | Vigila **inactividad**; avisa cuando deje de producir salida.                |
| `C-a C-g`   | `vbell`             | Alterna entre campana audible y **campana visual**.                          |
| `C-a t`     | `time`              | Muestra hora y carga del sistema en la línea de mensajes.                     |
| `C-a x`     | `lockscreen`        | **Bloquea** el terminal (pide la contraseña del usuario).                     |
| `C-a C`     | `clear`             | Limpia la pantalla de la ventana.                                            |
| `C-a C-l`   | `redisplay`         | **Redibuja** la pantalla (útil si se corrompe la salida).                    |
| `C-a z`     | `suspend`           | Suspende `screen` (`fg` para reanudar).                                       |
| `C-a ?`     | `help`              | Muestra la **tabla de atajos** vigente.                                       |
| `C-a \`     | `quit`              | Termina la sesión y **mata todas** las ventanas (pide confirmación).         |

---

# Configuración (`~/.screenrc`)
Ajustes persistentes. Se cargan al arrancar cualquier sesión (salvo con `-c`). Directivas de uso frecuente:

| **Directiva**                     | **Efecto**                                                                          |
| --------------------------------- | ----------------------------------------------------------------------------------- |
| `escape ^Bb`                      | Cambia el prefijo a `Ctrl-b` (y el literal a `b`). Evita choques con `Ctrl-a` de la shell (Emacs/readline: inicio de línea). |
| `defscrollback 10000`             | Líneas de historial por ventana (por defecto ~100).                                |
| `startup_message off`             | Suprime el aviso de copyright al arrancar.                                          |
| `autodetach on`                   | Separa automáticamente (en vez de morir) si se cae el terminal.                     |
| `vbell off`                       | Desactiva la campana visual.                                                        |
| `altscreen on`                    | Restaura la pantalla previa al salir de programas *full-screen* (`vim`, `less`).   |
| `defutf8 on`                      | Activa UTF-8 por defecto en las ventanas.                                           |
| `term screen-256color`            | Anuncia soporte de 256 colores.                                                     |
| `caption always "%{= kw}%-w%{= BW}%n %t%{-}%+w"` | Barra por ventana (*caption*) siempre visible.                        |
| `hardstatus alwayslastline "..."` | Barra de estado en la última línea.                                                 |
| `bind s split`                    | (Re)asigna una tecla del prefijo a un comando.                                      |
| `screen -t nombre n cmd`          | Predefine ventanas al iniciar (número `n`, título y comando).                       |

> Si se cambia el prefijo con `escape`, todos los atajos de esta guía usan la **nueva** tecla en lugar de `C-a`.

---

# Referencia rápida (lo esencial)
Los atajos mínimos para el flujo diario. Prefijo `C-a`.

| **Tarea**              | **Atajo**            |
| ---------------------- | -------------------- |
| Nueva ventana          | `C-a c`              |
| Cerrar ventana         | `C-a k` (o `exit`)   |
| Siguiente / anterior   | `C-a n` / `C-a p`    |
| Ir a ventana N         | `C-a 0`…`C-a 9`      |
| Lista de ventanas      | `C-a "`              |
| Última ventana         | `C-a C-a`            |
| Renombrar ventana      | `C-a A`              |
| Separar (detach)       | `C-a d`              |
| Reenganchar            | `screen -r`          |
| Dividir horizontal / vertical | `C-a S` / `C-a \|` |
| Cambiar de región      | `C-a Tab`            |
| Cerrar región          | `C-a X`              |
| Modo copia / scrollback| `C-a [`              |
| Pegar                  | `C-a ]`              |
| Ayuda de atajos        | `C-a ?`              |
| Terminar la sesión     | `C-a \` (o cerrar todas las ventanas) |

---

# Notas y alternativas
- **`screen` vs `tmux`**: `tmux` es un multiplexor más moderno con prefijo `C-b`, mejor soporte de *splits* y *scripting*, y configuración más simple. `screen` viene preinstalado en más entornos legacy/embebidos y sobre líneas serie (`screen /dev/ttyUSB0 115200`), donde sigue siendo la opción por defecto.
- **Línea serie**: `screen /dev/ttyUSB0 115200` abre un terminal serie; se sale con `C-a k` o `C-a \`.
- **Choque con la shell**: en `bash`/`zsh` con readline, `Ctrl-a` mueve al inicio de línea. Dentro de `screen` ese atajo queda capturado; usa `C-a a` para enviarlo literal a la aplicación, o reasigna el prefijo con `escape`.
- Ver también: [[zsh]], [[Bash_cheatsheet]], [[OpenSSH]].
