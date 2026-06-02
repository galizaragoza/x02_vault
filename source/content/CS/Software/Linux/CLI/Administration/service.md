service es un wrapper portable para gestionar servicios del sistema independientemente del init en uso. En sistemas SysV/OpenRC ejecuta los scripts de `/etc/init.d/<nombre>` en un entorno predecible (limpia variables y `LANG`); en sistemas con systemd redirige las órdenes a `systemctl`. Su valor es ofrecer una sintaxis estable (`service NOMBRE ACCIÓN`) sobre distintos gestores. Es código heredado: en hosts modernos systemd, `systemctl` es la herramienta nativa y recomendada.

```
service SCRIPT COMMAND [OPTIONS]
service --status-all
service --help | --version
```

---

## Acciones (COMMAND) sobre un servicio

El COMMAND se pasa tal cual al script de init, por lo que el conjunto depende del script. Las acciones estándar LSB son:

| Acción | Descripción | Ejemplo |
|--------|-------------|---------|
| `start` | Inicia el servicio. | `service ssh start` |
| `stop` | Detiene el servicio. | `service ssh stop` |
| `restart` | Detiene y vuelve a iniciar el servicio. | `service nginx restart` |
| `reload` | Recarga la configuración sin reiniciar el proceso (si el servicio lo soporta). | `service nginx reload` |
| `force-reload` | Recarga la configuración; si no se soporta, reinicia. | `service apache2 force-reload` |
| `status` | Muestra el estado del servicio. Devuelve código de salida LSB (0 = en ejecución). | `service ssh status` |

---

## Opciones de la herramienta

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--status-all` | Ejecuta `status` sobre todos los scripts de `/etc/init.d`. Marca `[ + ]` activos, `[ - ]` inactivos, `[ ? ]` indeterminado. | `service --status-all` |
| `--full-restart` | Sinónimo de `stop` seguido de `start` (forzar reinicio completo). | `service apache2 --full-restart` |
| `-h` / `--help` | Muestra ayuda de uso. | `service --help` |
| `-V` / `--version` | Muestra la versión. | `service --version` |

---

## Comportamiento del entorno

| Característica | Descripción |
|---------------|-------------|
| Entorno limpio | Ejecuta el script con un entorno reducido: solo conserva `TERM`, `PATH`=`/sbin:/usr/sbin:/bin:/usr/bin` y `LANG`. Evita que variables del usuario alteren el servicio. |
| Redirección a systemd | Si el sistema usa systemd y la unidad existe como `.service` nativo, `service NOMBRE ACCIÓN` se traduce a `systemctl ACCIÓN NOMBRE.service`. |
| Códigos de salida | `status` sigue el estándar LSB: `0` activo, `3` parado, otros = error/indeterminado. |

---

## Casos de uso comunes

```bash
# Estado de un servicio concreto
service ssh status

# Reiniciar tras cambiar configuración
service nginx restart

# Recargar sin cortar conexiones activas
service nginx reload

# Listar el estado de todos los servicios init.d
service --status-all

# Filtrar solo los servicios activos
service --status-all 2>&1 | grep '\[ + \]'
```
