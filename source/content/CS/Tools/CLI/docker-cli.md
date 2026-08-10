[Docker cheatsheet](https://dockerlabs.collabnix.com/)
# Common
| **Common Commands** | **Descripción**                      | **Ejemplo práctico**                        |
| ------------------- | ------------------------------------ | ------------------------------------------- |
| `docker run`        | Crea y lanza contenedor              | `docker run -d -p 80:80 nginx`              |
| `docker exec`       | Ejecuta comando en contenedor activo | `docker exec -it web1 bash`                 |
| `docker ps`         | Lista contenedores (activos)         | `docker ps -a` (todos)                      |
| `docker build`      | Construye imagen desde Dockerfile    | `docker build -t miapp:v1 .`                |
| `docker pull`       | Descarga imagen de registry          | `docker pull alpine:latest`                 |
| `docker images`     | Lista imágenes locales               | `docker images --digests`                   |
| `docker login`      | Autenticación a registry             | `docker login ghcr.io`                      |
| `docker logout`     | Cierra sesión de registry            | `docker logout`                             |
| `docker search`     | Busca imágenes en Docker Hub         | `docker search --limit 10 kali`             |
| `docker version`    | Muestra versión cliente/servidor     | `docker version`                            |
| `docker info`       | Información completa del daemon      | `docker info --format '{{.ServerVersion}}'` |
## docker run
| Opción                        | Descripción                                           | Ejemplo                                                   |
| ----------------------------- | ----------------------------------------------------- | --------------------------------------------------------- |
| `-d`                          | Ejecuta en background (detached)                      | `docker run -d -p 4444:4444 kali-tools`                   |
| `-it`                         | Terminal interactiva + TTY                            | `docker run -it alpine sh`                                |
| `--rm`                        | Borra el contenedor al salir                          | `docker run --rm -it ubuntu bash`                         |
| `-p host:container`           | Mapea puerto host → contenedor                        | `docker run -p 80:80 nginx`                               |
| `-p 127.0.0.1:host:container` | Mapea solo a localhost (no expone externamente)       | `docker run -p 127.0.0.1:8080:80 webapp`                  |
| `-v /host:/container`         | Monta directorio del host en el contenedor            | `docker run -v /:/mnt --privileged -it kali`              |
| `--privileged`                | Da todos los capabilities de Linux (breakout clásico) | `docker run --privileged -v /:/host -it ubuntu`           |
| `--network host`              | Usa la red del host directamente (sin NAT)            | `docker run --network host -it kali nmap -sV 10.0.0.0/24` |
| `--pid=host`                  | Comparte espacio PID del host                         | `docker run --pid=host -it alpine kill -9 1`              |
| `--ipc=host`                  | Comparte memoria compartida/IPC del host              | `docker run --ipc=host -it vulnerable-app`                |
| `--cap-add=SYS_ADMIN`         | Añade capacidad específica                            | `docker run --cap-add=SYS_ADMIN -v /:/mnt -it ubuntu`     |
| `--name nombre`               | Asigna nombre fijo al contenedor                      | `docker run --name revshell -it ubuntu`                   |
| `-e VAR=value`                | Define variable de entorno                            | `docker run -e RHOST=10.10.10.10 -e LPORT=443 metasploit` |
| `--restart unless-stopped`    | Reinicia siempre salvo stop manual (persistencia)     | `docker run --restart unless-stopped -d revshell`         |
## docker exec
| Opción / Uso       | Descripción                                      | Ejemplo                          |
| ------------------ | ------------------------------------------------ | -------------------------------- |
| `-it`              | Shell interactiva dentro de contenedor corriendo | `docker exec -it web1 /bin/bash` |
| `-u 0` o `-u root` | Ejecuta como root dentro del contenedor          | `docker exec -u 0 db id`         |
| (sin opción)       | Ejecuta comando simple                           | `docker exec web1 cat /app/.env` |
## docker ps
| Comando              | Descripción                         | Ejemplo                                             |
| -------------------- | ----------------------------------- | --------------------------------------------------- |
| `docker ps`          | Muestra solo contenedores corriendo | `docker ps`                                         |
| `docker ps -a`       | Muestra todos (corriendo + parados) | `docker ps -a`                                      |
| `docker ps -q`       | Solo IDs (ideal para scripts)       | `docker ps -q \| xargs docker stop`                 |
| `docker ps --format` | Formato personalizado               | `docker ps --format "table {{.Names}}\t{{.Ports}}"` |
## docker logs
| Opción         | Descripción              | Ejemplo                       |     |
| -------------- | ------------------------ | ----------------------------- | --- |
| `-f`           | Follow en tiempo real    | `docker logs -f web1`         |     |
| `--tail N`     | Últimas N líneas         | `docker logs --tail 100 db`   |     |
| `--since "Xm"` | Logs desde hace X tiempo | `docker logs --since 10m app` |     |
## docker inspect
|Formato|Descripción|Ejemplo|
|---|---|---|
|`-f '{{.NetworkSettings.IPAddress}}'`|Obtiene IP interna del contenedor|`docker inspect -f '{{.NetworkSettings.IPAddress}}' web1`|
|`-f '{{range .Mounts}}{{.Source}}{{end}}'`|Lista rutas montadas desde el host|`docker inspect -f '{{range .Mounts}}{{.Source}}{{end}}' db`|
|`-f '{{json .Config.Env}}'`|Muestra variables de entorno (posibles creds)|`docker inspect -f '{{json .Config.Env}}' web1 \| jq`|
|`-f '{{.HostConfig.Privileged}}'`|Comprueba si es privileged|`docker inspect -f '{{.HostConfig.Privileged}}' web1`|
## docker cp
|Comando|Descripción|Ejemplo|
|---|---|---|
|`docker cp cont:/ruta .`|Copia del contenedor al host|`docker cp web1:/app/.env .`|
|`docker cp archivo cont:/ruta`|Copia del host al contenedor|`docker cp rev.sh web1:/tmp/`|
## docker commit/save/load
| Comando                      | Descripción                                  | Ejemplo                                 |
| ---------------------------- | -------------------------------------------- | --------------------------------------- |
| `docker commit cont imagen`  | Crea nueva imagen con cambios del contenedor | `docker commit web1 dirty-kali:rootkit` |
| `docker save img > file.tar` | Exporta imagen completa a .tar               | `docker save kali-tools > kali.tar`     |
| `docker load < file.tar`     | Carga imagen desde .tar                      | `docker load < kali.tar`                |


# Managament
| **Management Commands** | **Descripción**                     | **Ejemplo práctico**                   |
| ----------------------- | ----------------------------------- | -------------------------------------- |
| `docker builder prune`  | Limpia build cache                  | `docker builder prune -af`             |
| `docker buildx`         | Builder avanzado (multi-arch)       | `docker buildx create --use mybuilder` |
| `docker compose`        | Orquestación multi-contenedor ([[docker-compose]]) | `docker compose up -d`                 |
| `docker container ls`   | Igual que `docker ps`               | `docker container ls -q` (solo IDs)    |
| `docker context use`    | Cambia contexto                     | `docker context use prod`              |
| `docker image prune`    | Borra imágenes dangling             | `docker image prune -af`               |
| `docker network ls`     | Lista redes                         | `docker network ls`                    |
| `docker volume ls`      | Lista volúmenes                     | `docker volume ls`                     |
| `docker system df`      | Uso de disco                        | `docker system df -v`                  |
| `docker system prune`   | Limpieza masiva                     | `docker system prune -af --volumes`    |
# Container
| **Comandos individuales** | **Descripción**                  | **Ejemplo práctico**                        |
| ------------------------- | -------------------------------- | ------------------------------------------- |
| `attach`                  | Conecta terminal a contenedor    | `docker attach web1`                        |
| `commit`                  | Crea imagen desde contenedor     | `docker commit web1 miapp:prod`             |
| `cp`                      | Copia archivos contenedor ↔ host | `docker cp web1:/app/log.txt .`             |
| `create`                  | Crea contenedor sin iniciarlo    | `docker create --name db postgres`          |
| `events`                  | Eventos en tiempo real           | `docker events --filter 'type=container'`   |
| `export`                  | Exporta filesystem a .tar        | `docker export web1 > web1.tar`             |
| `history`                 | Historial de capas de imagen     | `docker history alpine:latest`              |
| `import`                  | Crea imagen desde .tar           | `docker import web1.tar miapp:new`          |
| `inspect`                 | Info detallada                   | `docker inspect web1`                       |
| `kill`                    | Mata contenedor                  | `docker kill web1`                          |
| `logs -f`                 | Follow logs                      | `docker logs -f db`                         |
| `port`                    | Muestra mapeo de puertos         | `docker port web1`                          |
| `rename`                  | Cambia nombre                    | `docker rename old newname`                 |
| `start`                   | Inicia                           |                                             |
| `restart`                 | Reinicia                         | `docker restart web1`                       |
| `rm`                      | Borra contenedor                 | `docker rm -f web1`                         |
| `rmi`                     | Borra imagen                     | `docker rmi alpine:latest`                  |
| `save` / `load`           | Guarda/carga imagen a .tar       | `docker save nginx > nginx.tar`             |
| `stats`                   | Recursos en vivo                 | `docker stats`                              |
| `stop`                    | Detiene contenedor               | `docker stop web1`                          |
| `top`                     | ps dentro del contenedor         | `docker top web1`                           |
| `tag`                     | Etiqueta imagen                  | `docker tag miapp:v1 registry/miapp:latest` |
# Options
| **Global Options**       | **Descripción**                            | **Ejemplo práctico**                                |
| ------------------------ | ------------------------------------------ | --------------------------------------------------- |
| `--config`               | Ruta de configuración cliente              | `docker --config ~/.docker-prod info`               |
| `-c, --context`          | Selecciona contexto                        | `docker -c prod ps`                                 |
| `-D, --debug`            | Modo debug                                 | `docker -D info`                                    |
| `-H, --host`             | Daemon remoto                              | `docker -H tcp://192.168.1.100:2376 ps`             |
| `-l, --log-level`        | Nivel de log (debug/info/warn/error/fatal) | `docker --log-level warn ps`                        |
| `--tls`                  | Usa TLS                                    | `docker --tls -H tcp://remote:2376 ps`              |
| `--tlscacert`            | CA personalizada                           | `--tlscacert /certs/ca.pem`                         |
| `--tlscert` / `--tlskey` | Certificado y clave cliente                | `--tlscert client-cert.pem --tlskey client-key.pem` |
| `--tlsverify`            | Verifica certificado remoto                | `docker --tlsverify -H tcp://remote:2376 ps`        |
| `-v, --version`          | Muestra versión                            | `docker -v`                                         |


# Protips
### Borrar todos los containers
```
docker rm -v -f $(docker ps -qa)
```