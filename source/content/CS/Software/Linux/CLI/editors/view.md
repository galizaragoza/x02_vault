`view` es Vim arrancado en modo solo-lectura: equivale exactamente a `vim -R` (o `vi -R` donde Vim no está instalado). Abre el fichero con la opción `'readonly'` activada para evitar sobrescrituras accidentales, lo que lo hace idóneo para inspeccionar logs, ficheros de configuración o código sin riesgo de modificarlos. Comparte la totalidad de comandos, modos, motions y opciones de [[vim]]; lo único que cambia es el comportamiento de guardado. Esta guía cubre sus opciones de arranque y las particularidades del modo solo-lectura.

```
view [opciones] [fichero...]
```

---

## Particularidad: modo solo-lectura

`view` activa `'readonly'`, no `'nomodifiable'`: el buffer **sí** puede editarse, pero Vim avisa y exige confirmación para escribir. No es un mecanismo de seguridad real, solo una salvaguarda.

| **Acción** | **Comando** | **Efecto** |
|------------|-------------|------------|
| Intentar guardar | `:w` | Falla con `E45: 'readonly' option is set`. |
| Forzar guardado | `:w!` | Escribe el fichero pese a `'readonly'`. |
| Quitar solo-lectura en la sesión | `:set noreadonly` | Permite `:w` normal a partir de ahí. |
| Bloquear de verdad la edición | `:set nomodifiable` | Impide incluso modificar el buffer. |
| Guardar como otro fichero | `:w otro.txt` | Escribe en ruta distinta sin tocar el original. |
| Salir sin guardar | `:q` | Sale (sin cambios no guardados pendientes). |
| Salir descartando cambios | `:q!` | Descarta el buffer modificado. |

## Opciones de arranque

| **Flag** | **Descripción** | **Ejemplo** |
|----------|-----------------|-------------|
| `-R` | Modo solo-lectura (ya implícito en `view`; refuerza). | `view -R fichero` |
| `-M` | Desactiva todas las modificaciones (`nomodifiable` + `nowrite`). | `view -M fichero` |
| `+<n>` | Sitúa el cursor en la línea `n`. | `view +120 /var/log/syslog` |
| `+/<patrón>` | Sitúa el cursor en la primera coincidencia del patrón. | `view +/error app.log` |
| `+<comando>` | Ejecuta un comando Ex al abrir. | `view "+set nu" fichero` |
| `-c <comando>` | Ejecuta un comando Ex tras cargar el primer fichero. | `view -c "set list" fichero` |
| `-O[n]` | Abre los ficheros en ventanas divididas verticalmente. | `view -O a.log b.log` |
| `-o[n]` | Abre los ficheros en ventanas divididas horizontalmente. | `view -o a.log b.log` |
| `-p[n]` | Abre cada fichero en una pestaña. | `view -p *.conf` |
| `-r [fichero]` | Recuperación a partir de un fichero swap. | `view -r notas.txt` |
| `-R -` | Lee el contenido desde la entrada estándar. | `dmesg \| view -` |
| `-u <vimrc>` | Usa otro fichero de configuración (`NONE` = ninguno). | `view -u NONE fichero` |
| `-n` | No crea fichero swap. | `view -n /var/log/syslog` |
| `-x` | Edita ficheros cifrados. | `view -x secreto.txt` |
| `-b` | Modo binario. | `view -b firmware.bin` |
| `-d <f1> <f2>` | Modo diff entre ficheros. | `view -d v1.conf v2.conf` |
| `-Z` | Modo restringido (sin shell ni comandos externos). | `view -Z fichero` |
| `--noplugin` | No carga plugins. | `view --noplugin fichero` |
| `--version` | Muestra versión y características. | `view --version` |
| `-h`, `--help` | Ayuda. | `view -h` |

## Notas

- Para grandes ficheros de log conviene `view -n fichero` (sin swap) y navegar con motions de Vim (`G`, `gg`, `/patrón`, `Ctrl-d`/`Ctrl-u`).
- `vimdiff` es a `view -d` lo que `view` es a `vim -R`: alias del mismo binario con opciones de arranque distintas.

## Relacionados

- [[vim]] — referencia completa de modos, motions, operadores, registros y configuración.
