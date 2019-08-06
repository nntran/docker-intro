# Formation Docker

Solutions des TP

## Documentation

- Commandes CLI docker : https://docs.docker.com/engine/reference/commandline/cli/
- Commandes CLI docker-compose : https://docs.docker.com/compose/reference/overview/
- Comandes CLI docker-machine : https://docs.docker.com/machine/reference/

## Commandes utiles

### Supprimer tous les containers

```sh
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

### Supprimer toutes les images

```sh
docker rmi $(docker ls -q)
docker rmi $(docker images -f 'dangling=true' -q)
```

### Seulement les images `none`

```sh
docker rmi $(docker images -f 'dangling=true' -q)
```

## TP 1 : Hello World

Dockerfile

```Dockerfile
FROM ntdtfr/hello-world
LABEL maintainer="..."
```

1. Construire l'image Docker

```sh
docker build --rm --tag <votre id docker hub>/hello-world .
```

2. Déployer l'application

```sh
docker run --rm -p 8080:5000 <votre id docker hub>/hello-world
```

Url pour tester : http://localhost:8080

3. Si ça fonctionne, on `push` sur le repos Docker Hub

```sh
docker login

docker push <votre id docker`hub>/hello-world
```

Exemple de traces :

```
The push refers to repository [docker.io/<votre id docker hub>/hello-world]
256a7af3acb1: Pushed
latest: digest: sha256:cbf0d4e021288ac28c60433a62e0ca9ee42e4a830dd4d8479afc0ad5b12b7115 size: 528
```

## TP 2 : Pinger

Dockerfile

```Dockerfile
FROM alpine
LABEL maintainer="..."
ARG SERVER_IP
ENV SERVER ${SERVER_IP:-localhost}
CMD ping $SERVER
```

1. Pour construire l'image Docker

```sh
docker build --rm=true --build-arg SERVER=localhost --tag <votre id docker hub>/pinger .
```

2. Pour exécuter l'application

```sh
docker run --name pinger -e SERVER=192.168.xxx.xxx <votre id docker hub>/pinger
```

3. Pour pusher l'image sur le repos Docker Hub

```sh
docker push <votre id docker hub>/pinger
```

## TP 3 : Exposition des ports

```sh
docker run --name hello-world -p 8080:5000 -d ntdtfr/hello-world
```

## TP 4 : Volume et persistence des données

```sh
docker run --name pic1 -p 8081:5000 -v /Users/ntran/Datas/pictures:/pic-viewer/static -d ntdtfr/pic-viewer
```

## TP 5 : Composition des services avec `docker-compose`

docker-compose.yml

```yaml
version: "3"
services:
  pic-viewer:
    image: ntdtfr/pic-viewer
    restart: always
    ports:
      - 8090:5000
    volumes:
      - /Users/ntran/Datas/pictures:/pic-viewer/static
```

## TP 6 : Docker Machine

### Création d'une machine virtuelle avec `docker-machine`

#### Pour macOS / Linux

```sh
docker-machine create --driver=virtualbox vm-web-server
```

#### Pour Windows

```sh
docker-machine create --driver=hyperv vm-web-server
```

### Déploiement de l'application `pic-viewer` sans mapping de volumes

```sh
eval $(docker-machine env vm-web-server)
docker run -d -p 8080:5000 --restart=always ntdtfr/pic-viewer
```

### Reset des variables d'environnement

```sh
eval $(docker-machine env -u)
```

## TP 7 : Swarm

docker-stack.yml

```yaml
version: "3"
services:
  pic-viewer:
    image: ntdtfr/pic-viewer
    ports:
      - 8090:5000
    deploy:
      replicas: 3
      restart_policy:
        condition: on-failure
```

```sh
master_ip=`docker-machine ip vm-web-server`
docker swarm init --advertise-addr $master_ip
```

```
Swarm initialized: current node (nm7prjsmmkev04h1jkq0asdv4) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-07dax26x6l3odl7xniex5taq0fke1noy7i5mqgvv54xje8yk35-2dmxak6hf6zm7w6ha3bsknf0f 192.168.99.148:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

```sh
docker stack deploy --compose-file docker-stack.yml web
```

```
Creating network web_default
Creating service web_pic-viewer
```

```sh
docker stack services web
```

s

```
ID                  NAME                MODE                REPLICAS            IMAGE                      PORTS
mfysi1tzwfgz        web_pic-viewer      replicated          3/3                 ntdtfr/pic-viewer:latest   *:8090->5000/tcp
```

```sh
docker service ls
```

```
ID                  NAME                MODE                REPLICAS            IMAGE                      PORTS
mfysi1tzwfgz        web_pic-viewer      replicated          3/3                 ntdtfr/pic-viewer:latest   *:8090->5000/tcp
```

```
docker service scale web_pic-viewer=5
```

```
web_pic-viewer scaled to 5
overall progress: 5 out of 5 tasks
1/5: running   [==================================================>]
2/5: running   [==================================================>]
3/5: running   [==================================================>]
4/5: running   [==================================================>]
5/5: running   [==================================================>]
verify: Service converged
```

```sh
docker service ls
```

```
ID                  NAME                MODE                REPLICAS            IMAGE                      PORTS
mfysi1tzwfgz        web_pic-viewer      replicated          5/5                 ntdtfr/pic-viewer:latest   *:8090->5000/tcp
```
