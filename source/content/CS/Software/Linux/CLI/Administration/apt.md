apt es la interfaz de línea de comandos de alto nivel para el sistema de gestión de paquetes de Debian y derivadas (Ubuntu, etc.). Unifica las funciones más usadas de `apt-get` y `apt-cache` en un único binario con salida más legible (color, barra de progreso) pensado para uso interactivo. Opera sobre paquetes `.deb` y los repositorios definidos en `/etc/apt/sources.list` y `/etc/apt/sources.list.d/`. Para scripting con interfaz estable se recomienda [[apt-get]].

Relacionado: [[apt-get]].

# Subcomandos

| **Subcomando**     | **Descripción**                                                          | **Ejemplo**                  |
| ------------------ | ------------------------------------------------------------------------ | ---------------------------- |
| `update`           | Refresca la lista de paquetes disponibles desde los repos.               | `apt update`                 |
| `upgrade`          | Actualiza paquetes sin eliminar ninguno ya instalado.                    | `apt upgrade`                |
| `full-upgrade`     | Actualiza y elimina paquetes si es necesario para resolver dependencias. | `apt full-upgrade`           |
| `install`          | Instala (o actualiza) paquetes.                                          | `apt install nginx`          |
| `reinstall`        | Reinstala paquetes ya presentes.                                         | `apt reinstall nginx`        |
| `remove`           | Elimina paquetes conservando sus archivos de configuración.              | `apt remove nginx`           |
| `purge`            | Elimina paquetes junto con sus archivos de configuración.                | `apt purge nginx`            |
| `autoremove`       | Elimina dependencias huérfanas instaladas automáticamente.               | `apt autoremove`             |
| `autopurge`        | `autoremove` + purga de la configuración.                               | `apt autopurge`              |
| `search`           | Busca paquetes por nombre y descripción (regex).                         | `apt search "^python3-"`     |
| `show`             | Muestra información detallada de un paquete.                             | `apt show nginx`             |
| `list`             | Lista paquetes (ver flags `--installed`, `--upgradable`).               | `apt list --upgradable`      |
| `depends`          | Lista las dependencias de un paquete.                                    | `apt depends nginx`          |
| `rdepends`         | Lista los paquetes que dependen de uno dado (inversa).                   | `apt rdepends nginx`         |
| `satisfy`          | Resuelve e instala una cadena de dependencias arbitraria.               | `apt satisfy "nginx (>= 1.18)"` |
| `download`         | Descarga el `.deb` sin instalarlo.                                       | `apt download nginx`         |
| `source`           | Descarga el código fuente del paquete.                                   | `apt source nginx`           |
| `build-dep`        | Instala las dependencias de compilación de un paquete fuente.            | `apt build-dep nginx`        |
| `changelog`        | Descarga y muestra el changelog del paquete.                            | `apt changelog nginx`        |
| `edit-sources`     | Abre los archivos de fuentes en `$EDITOR` con validación.               | `apt edit-sources`           |
| `clean`            | Vacía la caché local de paquetes `.deb` descargados.                    | `apt clean`                  |
| `autoclean`        | Elimina de la caché los `.deb` ya no descargables.                      | `apt autoclean`              |
| `policy`           | Muestra prioridades/pinning y orígenes de un paquete.                   | `apt policy nginx`           |
| `moo`              | Huevo de pascua (super cow powers).                                     | `apt moo`                    |

# Flags comunes

Aplicables a la mayoría de subcomandos.

| **Flag**                         | **Descripción**                                                       | **Ejemplo**                              |
| -------------------------------- | --------------------------------------------------------------------- | ---------------------------------------- |
| `-y`, `--yes`, `--assume-yes`    | Responde "sí" a todas las preguntas (no interactivo).                 | `apt install -y nginx`                   |
| `--assume-no`                    | Responde "no" automáticamente a las preguntas.                       | `apt full-upgrade --assume-no`           |
| `-s`, `--simulate`, `--dry-run`  | Simula la operación sin modificar el sistema.                        | `apt install -s nginx`                   |
| `-d`, `--download-only`          | Solo descarga los paquetes, no los instala.                         | `apt install -d nginx`                   |
| `-f`, `--fix-broken`             | Intenta reparar dependencias rotas.                                 | `apt install -f`                         |
| `-m`, `--fix-missing`, `--ignore-missing` | Continúa aunque falten paquetes/fuentes.                  | `apt install -m nginx`                   |
| `-q`, `--quiet`                  | Salida reducida (`-qq` aún menos, asume `-y` en scripts).           | `apt-get update -qq`                     |
| `-V`, `--verbose-versions`       | Muestra versiones completas de los paquetes.                        | `apt install -V nginx`                   |
| `-t`, `--target-release <rel>`   | Fija la *release* de destino (p. ej. `bookworm-backports`).         | `apt -t bullseye-backports install nginx`|
| `-o`, `--option <opt=val>`       | Establece una opción de configuración arbitraria.                  | `apt -o Acquire::http::Proxy=... update` |
| `-c`, `--config-file <archivo>`  | Usa un archivo de configuración alternativo.                       | `apt -c ./apt.conf update`               |
| `-h`, `--help`                   | Ayuda.                                                              | `apt -h`                                 |

# Flags de install / upgrade

Controlan resolución de dependencias y política de cambios.

| **Flag**                            | **Descripción**                                                    | **Ejemplo**                                  |
| ----------------------------------- | ------------------------------------------------------------------ | -------------------------------------------- |
| `--no-install-recommends`           | No instala paquetes recomendados.                                 | `apt install --no-install-recommends nginx`  |
| `--install-suggests`                | Instala también los paquetes sugeridos.                           | `apt install --install-suggests nginx`       |
| `--no-upgrade`                      | En `install`, no actualiza si el paquete ya está instalado.       | `apt install --no-upgrade nginx`             |
| `--only-upgrade`                    | Solo actualiza; no instala paquetes nuevos.                       | `apt install --only-upgrade nginx`           |
| `--reinstall`                       | Reinstala aunque ya esté a la última versión.                     | `apt install --reinstall nginx`              |
| `--purge`                           | En `remove`, elimina también la configuración.                    | `apt remove --purge nginx`                   |
| `--autoremove`                      | Elimina huérfanos como parte de la operación.                     | `apt install --autoremove nginx`             |
| `--allow-downgrades`                | Permite instalar una versión anterior.                            | `apt install --allow-downgrades nginx=1.18`  |
| `--allow-change-held-packages`      | Permite cambiar paquetes marcados como retenidos (`hold`).        | `apt install --allow-change-held-packages x` |
| `--allow-remove-essential`          | Permite eliminar paquetes esenciales (peligroso).                | `apt remove --allow-remove-essential x`      |
| `--allow-unauthenticated`           | Instala paquetes sin firma verificada (inseguro).                | `apt install --allow-unauthenticated x`      |
| `--no-download`                     | No descarga; usa solo lo presente en la caché.                   | `apt install --no-download nginx`            |

# Flags de list / search

| **Flag**            | **Descripción**                                          | **Ejemplo**                  |
| ------------------- | -------------------------------------------------------- | ---------------------------- |
| `--installed`       | Lista solo paquetes instalados.                          | `apt list --installed`       |
| `--upgradable`      | Lista paquetes con actualización disponible.             | `apt list --upgradable`      |
| `--all-versions`    | Muestra todas las versiones disponibles de cada paquete. | `apt list --all-versions nginx` |
| `--manual-installed`| Lista paquetes instalados manualmente.                   | `apt list --manual-installed`|
| `-n`, `--names-only`| Busca solo en nombres (en `search`).                    | `apt search -n nginx`        |
| `-f`, `--full`      | Muestra registros completos en `search`.                | `apt search -f nginx`        |

# Notas operativas

- `apt` advierte explícitamente: *"this APT has Super Cow Powers"* y que su CLI **no es estable entre versiones**; para scripts usar [[apt-get]].
- Flujo típico de actualización: `apt update && apt full-upgrade`.
- Retener un paquete (evitar su actualización): `apt-mark hold <pkg>` (binario `apt-mark`).
- Limpieza de huérfanos y caché: `apt autoremove --purge && apt clean`.
- Tras añadir un repo, refrescar siempre con `apt update` antes de instalar.
