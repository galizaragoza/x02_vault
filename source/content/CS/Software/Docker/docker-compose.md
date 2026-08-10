# Docker Compose

**Docker Compose** define y ejecuta aplicaciones multi-contenedor a partir de un fichero declarativo (`compose.yaml` / `docker-compose.yml`) que describe servicios, redes, volúmenes y su configuración. Con un solo comando levanta, para, reconstruye y escala todo el stack, manteniendo el estado agrupado bajo un *proyecto*. Desde Compose v2 es un **plugin del CLI de Docker** invocado como `docker compose` (con espacio), reemplazando al binario `docker-compose` en Python. Referencia: Docker Compose v2 / CLI Docker.

```
docker compose [opciones-globales] SUBCOMANDO [opciones] [SERVICIO...]
```

Ver también el CLI base en [[docker-cli]] y los fundamentos en [[Docker 101]].

---

## Opciones globales

Van **antes** del subcomando; seleccionan fichero, proyecto y comportamiento general.

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `-f`, `--file` | Fichero(s) Compose a usar. Repetible: se fusionan en orden (overrides). | `docker compose -f compose.yaml -f compose.prod.yaml up` |
| `-p`, `--project-name` | Nombre del proyecto (prefijo de contenedores/redes). | `docker compose -p staging up -d` |
| `--profile` | Activa perfiles de servicios (`profiles:`). Repetible. | `docker compose --profile debug up` |
| `--env-file` | Fichero de variables de entorno alternativo. | `docker compose --env-file .env.prod up` |
| `--project-directory` | Directorio de trabajo base para rutas relativas. | `docker compose --project-directory ./app up` |
| `--parallel` | Máximo de operaciones en paralelo (`-1` = ilimitado). | `docker compose --parallel 4 pull` |
| `--progress` | Tipo de salida de progreso: `auto`, `tty`, `plain`, `json`, `quiet`. | `docker compose --progress plain build` |
| `--ansi` | Control de códigos ANSI: `never`, `always`, `auto`. | `docker compose --ansi never up` |
| `--compatibility` | Modo de compatibilidad con Compose v1. | `docker compose --compatibility up` |
| `--all-resources` | Incluye recursos no usados por servicios. | `docker compose --all-resources down` |
| `--dry-run` | Simula la ejecución sin aplicar cambios. | `docker compose --dry-run up` |

---

## `up` — crear y arrancar servicios

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `-d`, `--detach` | Ejecuta en segundo plano. | `docker compose up -d` |
| `--build` | Reconstruye imágenes antes de arrancar. | `docker compose up --build` |
| `--no-build` | No construye aunque la política lo indique. | `docker compose up --no-build` |
| `--force-recreate` | Recrea contenedores aunque no cambie su config. | `docker compose up --force-recreate` |
| `--no-recreate` | No recrea contenedores existentes. | `docker compose up --no-recreate` |
| `--no-deps` | No arranca servicios enlazados (dependencias). | `docker compose up --no-deps web` |
| `--no-start` | Crea los contenedores pero no los arranca. | `docker compose up --no-start` |
| `--scale` | Escala un servicio a N réplicas. | `docker compose up --scale worker=3` |
| `--remove-orphans` | Elimina contenedores de servicios ya no definidos. | `docker compose up --remove-orphans` |
| `-V`, `--renew-anon-volumes` | Recrea volúmenes anónimos en vez de reusarlos. | `docker compose up -V` |
| `--pull` | Política de pull previa: `always`, `missing`, `never`. | `docker compose up --pull always` |
| `--abort-on-container-exit` | Para todo si algún contenedor termina. | `docker compose up --abort-on-container-exit` |
| `--abort-on-container-failure` | Para todo si algún contenedor falla. | `docker compose up --abort-on-container-failure` |
| `--exit-code-from` | Devuelve el exit code de un servicio concreto. | `docker compose up --exit-code-from tests` |
| `--attach` / `--no-attach` | Adjunta / no adjunta logs de servicios concretos. | `docker compose up --no-attach db` |
| `--attach-dependencies` | Adjunta también los logs de las dependencias. | `docker compose up --attach-dependencies` |
| `--wait` / `--wait-timeout` | Espera a que estén running/healthy (con timeout). | `docker compose up -d --wait --wait-timeout 60` |
| `-w`, `--watch` | Modo watch: reconstruye al cambiar el código. | `docker compose up --watch` |
| `-t`, `--timeout` | Timeout (s) para parar contenedores existentes. | `docker compose up -t 30` |
| `--timestamps` | Muestra marcas de tiempo en logs. | `docker compose up --timestamps` |
| `--no-color` / `--no-log-prefix` | Salida monocroma / sin prefijo de servicio. | `docker compose up --no-log-prefix` |
| `--quiet-pull` / `--quiet-build` | Pull/build sin salida de progreso. | `docker compose up --quiet-pull` |
| `-y`, `--yes` | Asume "sí" a todas las confirmaciones. | `docker compose up -y` |

---

## `down` — parar y eliminar

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `-v`, `--volumes` | Elimina también los volúmenes con nombre declarados. | `docker compose down -v` |
| `--rmi` | Elimina imágenes: `local` (sin tag custom) o `all`. | `docker compose down --rmi all` |
| `--remove-orphans` | Elimina contenedores huérfanos. | `docker compose down --remove-orphans` |
| `-t`, `--timeout` | Timeout de apagado en segundos. | `docker compose down -t 5` |

---

## `build` — construir imágenes

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `--build-arg` | Variables de build (`ARG`). Repetible. | `docker compose build --build-arg VER=1.2` |
| `--no-cache` | No usa cache de capas. | `docker compose build --no-cache` |
| `--pull` | Intenta siempre bajar una base más reciente. | `docker compose build --pull` |
| `--push` | Publica las imágenes tras construir. | `docker compose build --push` |
| `--builder` | Selecciona el builder de BuildKit. | `docker compose build --builder mybuilder` |
| `-m`, `--memory` | Límite de memoria del contenedor de build. | `docker compose build -m 2g` |
| `--ssh` | Reenvía agentes SSH al build. | `docker compose build --ssh default` |
| `--with-dependencies` | Construye también las dependencias. | `docker compose build --with-dependencies` |
| `--check` / `--print` | Valida la config / imprime el bake equivalente. | `docker compose build --check` |
| `--provenance` / `--sbom` | Añade attestations de procedencia / SBOM. | `docker compose build --sbom true` |
| `-q`, `--quiet` | Suprime la salida de build. | `docker compose build -q` |

---

## `run` — comando puntual (one-off)

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `--rm` | Elimina el contenedor al terminar. | `docker compose run --rm web pytest` |
| `-d`, `--detach` | Ejecuta en segundo plano. | `docker compose run -d worker` |
| `-i`, `--interactive` | Mantiene STDIN abierto. | `docker compose run -i web sh` |
| `-T`, `--no-tty` | Deshabilita el pseudo-TTY (scripts/pipes). | `docker compose run -T db psql` |
| `--entrypoint` | Sobrescribe el entrypoint. | `docker compose run --entrypoint sh web` |
| `-e`, `--env` | Variable de entorno. Repetible. | `docker compose run -e DEBUG=1 web` |
| `--env-from-file` | Variables desde fichero. | `docker compose run --env-from-file .env web` |
| `-l`, `--label` | Añade/sobrescribe una etiqueta. | `docker compose run -l job=migrate web` |
| `--name` | Nombre del contenedor. | `docker compose run --name migrate web` |
| `-u`, `--user` | Usuario/uid de ejecución. | `docker compose run -u root web id` |
| `-w`, `--workdir` | Directorio de trabajo dentro del contenedor. | `docker compose run -w /app web ls` |
| `-v`, `--volume` | Bind mount adicional. | `docker compose run -v $PWD:/src web` |
| `-p`, `--publish` | Publica puertos al host. | `docker compose run -p 8080:80 web` |
| `-P`, `--service-ports` | Usa los puertos definidos en el servicio. | `docker compose run -P web` |
| `--build` | Construye la imagen antes de ejecutar. | `docker compose run --build web` |
| `--no-deps` | No arranca dependencias. | `docker compose run --no-deps web sh` |
| `--cap-add` / `--cap-drop` | Añade/quita capacidades Linux. | `docker compose run --cap-add NET_ADMIN web` |
| `--use-aliases` | Usa los alias de red del servicio. | `docker compose run --use-aliases web` |
| `--pull` | Política de pull previa. | `docker compose run --pull missing web` |
| `--remove-orphans` | Elimina huérfanos. | `docker compose run --remove-orphans web` |

---

## `exec` — ejecutar en contenedor activo

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `-d`, `--detach` | Ejecuta en segundo plano. | `docker compose exec -d web touch /tmp/x` |
| `-e`, `--env` | Variable de entorno. | `docker compose exec -e TZ=UTC web date` |
| `-u`, `--user` | Usuario de ejecución. | `docker compose exec -u root web bash` |
| `-w`, `--workdir` | Directorio de trabajo. | `docker compose exec -w /app web ls` |
| `-T`, `--no-tty` | Sin pseudo-TTY (pipes/scripts). | `docker compose exec -T db psql < dump.sql` |
| `--index` | Índice de réplica si el servicio tiene varias. | `docker compose exec --index 2 worker sh` |
| `--privileged` | Privilegios extendidos para el proceso. | `docker compose exec --privileged web bash` |

---

## `logs` — ver salida

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `-f`, `--follow` | Sigue la salida en tiempo real. | `docker compose logs -f web` |
| `-n`, `--tail` | Número de líneas finales a mostrar. | `docker compose logs --tail 100 db` |
| `-t`, `--timestamps` | Añade marcas de tiempo. | `docker compose logs -t` |
| `--since` / `--until` | Filtra por timestamp. | `docker compose logs --since 10m web` |
| `--no-color` / `--no-log-prefix` | Monocromo / sin prefijo de servicio. | `docker compose logs --no-log-prefix` |
| `--index` | Índice de réplica concreta. | `docker compose logs --index 1 worker` |

---

## `ps` / `ls` — listar contenedores y proyectos

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `-a`, `--all` | Incluye contenedores/proyectos parados. | `docker compose ps -a` |
| `-q`, `--quiet` | Solo IDs (ps) / nombres de proyecto (ls). | `docker compose ps -q` |
| `--services` | Lista solo los nombres de servicio (ps). | `docker compose ps --services` |
| `--filter` | Filtra por propiedad/condición. | `docker compose ps --filter status=running` |
| `--status` | Filtra por estado (`running`, `exited`, `paused`...). | `docker compose ps --status exited` |
| `--format` | Formato de salida (`table`, `json`, plantilla). | `docker compose ps --format json` |
| `--no-trunc` | No trunca la salida. | `docker compose ps --no-trunc` |
| `--orphans` | Incluye servicios huérfanos (ps). | `docker compose ps --orphans` |

---

## `config` — validar y renderizar

| Opción | Función | Ejemplo |
| --- | --- | --- |
| `-q`, `--quiet` | Solo valida, sin imprimir. | `docker compose config -q` |
| `--services` / `--profiles` / `--images` | Lista servicios / perfiles / imágenes. | `docker compose config --services` |
| `--hash` | Imprime el hash de config por servicio. | `docker compose config --hash '*'` |
| `--format` | Formato de salida (`yaml`, `json`). | `docker compose config --format json` |
| `-o`, `--output` | Guarda a fichero. | `docker compose config -o resolved.yaml` |
| `--no-interpolate` | No interpola variables de entorno. | `docker compose config --no-interpolate` |
| `--resolve-image-digests` | Fija tags a digests inmutables. | `docker compose config --resolve-image-digests` |
| `--environment` | Imprime el entorno usado en la interpolación. | `docker compose config --environment` |

---

## Ciclo de vida: `start` / `stop` / `restart` / `pause` / `unpause` / `kill`

| Subcomando / Opción | Función | Ejemplo |
| --- | --- | --- |
| `start` | Arranca servicios ya creados. | `docker compose start web` |
| `start --wait` / `--wait-timeout` | Espera a running/healthy. | `docker compose start --wait` |
| `stop` | Para servicios sin eliminarlos. | `docker compose stop` |
| `stop -t`, `--timeout` | Timeout de apagado. | `docker compose stop -t 5` |
| `restart` | Reinicia contenedores. | `docker compose restart web` |
| `restart --no-deps` | Sin reiniciar dependencias. | `docker compose restart --no-deps web` |
| `pause` / `unpause` | Congela / reanuda procesos del servicio. | `docker compose pause db` |
| `kill -s`, `--signal` | Envía una señal (default SIGKILL). | `docker compose kill -s SIGTERM web` |
| `kill --remove-orphans` | Mata y limpia huérfanos. | `docker compose kill --remove-orphans` |

---

## Imágenes y registro: `pull` / `push` / `images`

| Subcomando / Opción | Función | Ejemplo |
| --- | --- | --- |
| `pull` | Descarga las imágenes de los servicios. | `docker compose pull` |
| `pull --policy` | Política: `missing`, `always`. | `docker compose pull --policy always` |
| `pull --include-deps` | Incluye dependencias. | `docker compose pull --include-deps` |
| `pull --ignore-pull-failures` | Continúa pese a fallos. | `docker compose pull --ignore-pull-failures` |
| `push` | Publica las imágenes. | `docker compose push` |
| `push --include-deps` | Incluye imágenes de dependencias. | `docker compose push --include-deps` |
| `images` | Lista imágenes de los contenedores creados. | `docker compose images` |
| `images -q` / `--format` | Solo IDs / formato. | `docker compose images --format json` |

---

## Escalado, transferencia y varios

| Subcomando / Opción | Función | Ejemplo |
| --- | --- | --- |
| `scale SERVICIO=N` | Escala servicios a N réplicas. | `docker compose scale worker=5` |
| `scale --no-deps` | Sin arrancar dependencias. | `docker compose scale worker=3 --no-deps` |
| `create` | Crea contenedores sin arrancarlos. | `docker compose create` |
| `create --build` / `--force-recreate` | Construye / recrea al crear. | `docker compose create --build` |
| `rm` | Elimina contenedores parados. | `docker compose rm` |
| `rm -f`, `--force` | Sin confirmación. | `docker compose rm -f` |
| `rm -s`, `--stop` | Para antes de eliminar. | `docker compose rm -sf` |
| `rm -v`, `--volumes` | Elimina volúmenes anónimos. | `docker compose rm -v` |
| `cp` | Copia ficheros host ↔ contenedor. | `docker compose cp web:/app/log.txt ./` |
| `cp -a`, `--archive` | Modo archivo (preserva uid/gid). | `docker compose cp -a ./src web:/app` |
| `cp --index` | Réplica concreta. | `docker compose cp --index 2 worker:/data ./` |
| `top` | Procesos en ejecución por servicio. | `docker compose top` |
| `port SERVICIO PUERTO` | Puerto público de un binding. | `docker compose port web 80` |
| `port --protocol` | Protocolo: `tcp`/`udp`. | `docker compose port --protocol udp dns 53` |
| `stats` | Uso de recursos en vivo. | `docker compose stats --no-stream` |
| `events` | Stream de eventos en tiempo real. | `docker compose events --json` |
| `wait` | Bloquea hasta que los contenedores paren. | `docker compose wait web` |
| `wait --down-project` | Tira el proyecto al parar el primero. | `docker compose wait --down-project web` |
| `watch` | Vigila el código y reconstruye/refresca. | `docker compose watch` |
| `watch --no-up` / `--prune` | No arranca antes / poda imágenes colgantes. | `docker compose watch --no-up` |
| `version` | Versión de Compose. | `docker compose version --short` |

---

## Estructura mínima de `compose.yaml`

```yaml
services:
  web:
    build: .
    ports:
      - "8080:80"
    environment:
      - DEBUG=0
    depends_on:
      - db
    profiles: ["prod"]
  db:
    image: postgres:16
    volumes:
      - dbdata:/var/lib/postgresql/data
volumes:
  dbdata:
```

---

## Casos prácticos

```bash
# Levantar en background, construir y esperar healthchecks
docker compose up -d --build --wait

# Override de producción sobre la base
docker compose -f compose.yaml -f compose.prod.yaml up -d

# Ejecutar migraciones one-off y limpiar
docker compose run --rm web ./manage.py migrate

# Seguir logs de un servicio con timestamps
docker compose logs -f -t web

# Validar y resolver la config final con digests fijados
docker compose config --resolve-image-digests -o resolved.yaml

# Tirar todo incluyendo volúmenes e imágenes locales
docker compose down -v --rmi local --remove-orphans
```

Ver también [[docker-cli]] · [[Docker 101]] · [[Kubernetes]].
