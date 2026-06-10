apt-get es la interfaz de línea de comandos de bajo nivel del sistema APT en Debian y derivadas. A diferencia de [[apt]], su interfaz y formato de salida son **estables entre versiones**, por lo que es la herramienta recomendada para scripts y automatización. Gestiona la instalación, actualización y eliminación de paquetes `.deb` resolviendo dependencias a partir de los repositorios de `/etc/apt/sources.list`. Sus funciones de consulta (búsqueda, info) están en el binario hermano `apt-cache`.

Relacionado: [[apt]].

# Subcomandos

| **Subcomando**     | **Descripción**                                                          | **Ejemplo**                  |
| ------------------ | ------------------------------------------------------------------------ | ---------------------------- |
| `update`           | Refresca la lista de paquetes disponibles desde los repos.               | `apt-get update`             |
| `upgrade`          | Actualiza paquetes sin eliminar ninguno instalado.                       | `apt-get upgrade`            |
| `dist-upgrade`     | Actualiza gestionando cambios de dependencias (instala/elimina).         | `apt-get dist-upgrade`       |
| `full-upgrade`     | Sinónimo de `dist-upgrade`.                                              | `apt-get full-upgrade`       |
| `dselect-upgrade`  | Aplica las selecciones marcadas en `dselect`.                            | `apt-get dselect-upgrade`    |
| `install`          | Instala o actualiza paquetes.                                           | `apt-get install nginx`      |
| `reinstall`        | Reinstala paquetes ya presentes.                                        | `apt-get reinstall nginx`    |
| `remove`           | Elimina paquetes conservando su configuración.                          | `apt-get remove nginx`       |
| `purge`            | Elimina paquetes junto con su configuración.                            | `apt-get purge nginx`        |
| `autoremove`       | Elimina dependencias huérfanas instaladas automáticamente.              | `apt-get autoremove`         |
| `source`           | Descarga el código fuente del paquete.                                  | `apt-get source nginx`       |
| `build-dep`        | Instala las dependencias de compilación de un paquete fuente.           | `apt-get build-dep nginx`    |
| `satisfy`          | Resuelve e instala una cadena de dependencias arbitraria.              | `apt-get satisfy "nginx (>= 1.18)"` |
| `check`            | Verifica que no haya dependencias rotas.                               | `apt-get check`              |
| `download`         | Descarga el `.deb` sin instalarlo.                                     | `apt-get download nginx`     |
| `changelog`        | Descarga y muestra el changelog del paquete.                          | `apt-get changelog nginx`    |
| `clean`            | Vacía la caché local de `.deb` descargados.                           | `apt-get clean`              |
| `autoclean`        | Elimina de la caché los `.deb` ya no descargables.                    | `apt-get autoclean`          |
| `indextargets`     | Muestra los índices/objetivos de adquisición disponibles.            | `apt-get indextargets`       |

# Flags comunes

| **Flag**                         | **Descripción**                                                       | **Ejemplo**                              |
| -------------------------------- | --------------------------------------------------------------------- | ---------------------------------------- |
| `-y`, `--yes`, `--assume-yes`    | Responde "sí" a las preguntas (no interactivo).                       | `apt-get install -y nginx`               |
| `--assume-no`                    | Responde "no" automáticamente.                                       | `apt-get upgrade --assume-no`            |
| `-s`, `--simulate`, `--dry-run`, `--just-print`, `--no-act` | Simula sin modificar el sistema.          | `apt-get install -s nginx`               |
| `-d`, `--download-only`          | Solo descarga, no instala.                                          | `apt-get install -d nginx`               |
| `-f`, `--fix-broken`             | Repara dependencias rotas.                                         | `apt-get install -f`                     |
| `-m`, `--ignore-missing`, `--fix-missing` | Continúa aunque falten paquetes/fuentes.                 | `apt-get install -m nginx`               |
| `-q`, `--quiet`                  | Salida apta para logs (`-qq` implica `-y`, sin progreso).          | `apt-get -qq update`                     |
| `-V`, `--verbose-versions`       | Muestra versiones completas.                                       | `apt-get -V install nginx`               |
| `-t`, `--target-release`, `--default-release <rel>` | Fija la *release* de destino.                  | `apt-get -t bookworm-backports install x`|
| `-o`, `--option <opt=val>`       | Establece una opción de configuración arbitraria.                 | `apt-get -o Dpkg::Options::="--force-confnew" install x` |
| `-c`, `--config-file <archivo>`  | Usa un archivo de configuración alternativo.                      | `apt-get -c ./apt.conf update`           |
| `-h`, `--help`                   | Ayuda.                                                            | `apt-get -h`                             |
| `-v`, `--version`                | Versión.                                                          | `apt-get -v`                             |

# Flags de install / upgrade

| **Flag**                            | **Descripción**                                                    | **Ejemplo**                                  |
| ----------------------------------- | ------------------------------------------------------------------ | -------------------------------------------- |
| `--no-install-recommends`           | No instala paquetes recomendados.                                 | `apt-get install --no-install-recommends x`  |
| `--install-suggests`                | Instala también los sugeridos.                                    | `apt-get install --install-suggests x`       |
| `--no-upgrade`                      | En `install`, no actualiza si ya está instalado.                  | `apt-get install --no-upgrade nginx`         |
| `--only-upgrade`                    | Solo actualiza; no instala paquetes nuevos.                       | `apt-get install --only-upgrade nginx`       |
| `--reinstall`                       | Reinstala aunque ya esté a la última versión.                     | `apt-get install --reinstall nginx`          |
| `--purge`                           | En `remove`, elimina también la configuración.                    | `apt-get remove --purge nginx`               |
| `--auto-remove`, `--autoremove`     | Elimina huérfanos como parte de la operación.                     | `apt-get install --auto-remove nginx`        |
| `--allow-downgrades`                | Permite instalar una versión anterior.                            | `apt-get install --allow-downgrades nginx=1.18` |
| `--allow-change-held-packages`      | Permite cambiar paquetes retenidos (`hold`).                      | `apt-get install --allow-change-held-packages x` |
| `--allow-remove-essential`          | Permite eliminar paquetes esenciales (peligroso).               | `apt-get remove --allow-remove-essential x`  |
| `--allow-unauthenticated`           | Instala paquetes sin firma verificada (inseguro).               | `apt-get install --allow-unauthenticated x`  |
| `--no-download`                     | No descarga; usa solo la caché.                                  | `apt-get install --no-download nginx`        |
| `--purge` + `autoremove`            | Combina purga con limpieza de huérfanos.                         | `apt-get --purge autoremove`                 |

# Flags de source / build-dep

| **Flag**                | **Descripción**                                                       | **Ejemplo**                       |
| ----------------------- | --------------------------------------------------------------------- | --------------------------------- |
| `-b`, `--compile`, `--build` | Compila el paquete fuente tras descargarlo.                      | `apt-get -b source nginx`         |
| `--no-build`            | No compila; solo descarga las fuentes.                              | `apt-get source --no-build nginx` |
| `--diff-only`           | Descarga solo el archivo diff de las fuentes.                       | `apt-get source --diff-only nginx`|
| `--tar-only`            | Descarga solo el tarball de las fuentes.                            | `apt-get source --tar-only nginx` |
| `--dsc-only`            | Descarga solo el archivo `.dsc`.                                   | `apt-get source --dsc-only nginx` |
| `--arch-only`           | En `build-dep`, instala solo dependencias específicas de arquitectura. | `apt-get build-dep --arch-only x` |
| `--indep-only`          | Instala solo dependencias independientes de arquitectura.          | `apt-get build-dep --indep-only x`|
| `--host-architecture <arch>` | Arquitectura de destino para cross-building.                  | `apt-get build-dep --host-architecture armhf x` |

# Opciones útiles vía -o

Configuración común que suele pasarse con `-o` en automatización.

| **Opción**                                         | **Descripción**                                              | **Ejemplo**                                            |
| -------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| `Dpkg::Options::="--force-confold"`                | Conserva los archivos de configuración existentes.           | `apt-get -o Dpkg::Options::="--force-confold" upgrade` |
| `Dpkg::Options::="--force-confnew"`                | Usa los archivos de configuración nuevos del paquete.        | `apt-get -o Dpkg::Options::="--force-confnew" upgrade` |
| `Acquire::http::Proxy="http://host:port"`          | Define un proxy para la descarga.                            | `apt-get -o Acquire::http::Proxy="http://10.0.0.1:3128" update` |
| `APT::Get::Assume-Yes=true`                        | Equivale a `-y`.                                            | `apt-get -o APT::Get::Assume-Yes=true install nginx`   |
| `Dir::Etc::sourcelist="./mi.list"`                 | Usa una lista de fuentes alternativa.                       | `apt-get -o Dir::Etc::sourcelist="./mi.list" update`   |

# Notas operativas

- Interfaz **estable**: preferir `apt-get` (y `apt-cache` para consultas) en scripts; `apt` solo para uso interactivo.
- `DEBIAN_FRONTEND=noninteractive apt-get -y install …` evita prompts de `debconf` en automatización.
- Diferencia `upgrade` vs `dist-upgrade`: el primero nunca elimina paquetes; el segundo sí, para resolver dependencias nuevas.
- Reparar un sistema con dependencias rotas: `apt-get -f install` o `dpkg --configure -a`.
- Búsqueda e info de paquetes: `apt-cache search <re>`, `apt-cache show <pkg>`, `apt-cache policy <pkg>`.
