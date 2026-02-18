```shell
# Limpiador de contenedores
cleanDocker () {
docker rm $(docker ps -a -q) --force 2>/dev/null
docker rmi $(docker images -a -q) --force 2>/dev/null
docker volume rm $(docker volume ls -q) --force 2>/dev/null
docker network rm $(docker network ls -a -q) --force 2>/dev/null
}

```
