# Travaux pratiques (TP)

## Commandes utiles

### Supprimer tous les conteneurs (containers) Docker

```sh
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

### Supprimer toutes les images Docker

```sh
docker rmi $(docker ls -q)
docker rmi $(docker images -f 'dangling=true' -q)
```

### Supprimer seulement les images `none`

```sh
docker rmi $(docker images -f 'dangling=true' -q)
```

### Conseils

Voici quelques conseils à prendre en compte avant de vous lancer dans la réalisation des TP ci-après.

- [x] Pensez à créer un sous répertoire pour stocker vos fichiers pour chaque TP. 
- [ ] Munissez-vous d'un éditeur qui reconnait la syntaxe Dockerfile. 



## TP 1 : Hello World

**Objectifs :** 
 - Créer une image docker en se basant sur l'image officielle `hello-world` existante. 
 - Faire un test d'exécution avec la commande `docker run ...`.
 - Pousser l'image dans le dépôt de Docker Hub.

**Prérquis :**
- [x] Avoir un compte [Docker Hub](https://hub.docker.com)
- [x] Connaître les commandes basiques de Docker
- [x] Connaître les instructions basiques d'écriture d'un Dockerfile


Commencez par créer le fichier [Dockerfile](hello-world/Dockerfile) :
 
<details open>
  <summary>Dockerfile</summary>


```Dockerfile
FROM hello-world
LABEL maintainer="..."
```

</details>

Utilisez la commande ci-dessous pour construire votre image : 

```sh
docker build --rm --tag <votre-id-docker-hub>/hello-world .
```

Faites un test d'exécution :

```sh
docker run <votre-id-docker-hub>/hello-world
```

Si tout se passe bien (il n'y a aucune raison que cela se passe mal ;)), poussez votre image un sur votre dépôt `Docker Hub` :

```sh
docker login

docker push <votre-id-docker-hub>/hello-world
```

## TP 2 : Pinger



<details open>
  <summary>Dockerfile</summary>

```Dockerfile
FROM alpine
LABEL maintainer="..."
ARG SERVER_IP
ENV SERVER ${SERVER_IP:-localhost}
CMD ping $SERVER
```

</details>

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

## TP 4 : Volume et persistance des données

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

**Objectifs** : Créez une machine virtuelle (VM) avec `docker-machine` pour hégerber l'application `pic-viewer`. Nommez cette VM `vm-web-server`.
`

### **Etape 1**: Commencez par créer la VM `vm-web-server`

#### Pour MacOS / Linux (Virtualbox)

```sh
docker-machine create --driver=virtualbox vm-web-server
```

#### Pour Windows (Hyper-V)

Créez d'abord un commutateur virtuel (virtual switch) depuis le Gestionnaire Hyper-V. Nommez le commutateur `form-docker` et sélectionnez `Réseau externe` comme type de connexion :

![](./images/vitrual-switch.png)

Une fois que le commutateur créé, ouvrez un terminal Powershell en mode administrateur et exécutez la commande ci-dessous :

```sh
docker-machine create --driver hyperv --hyperv-virtual-switch form-docker vm-web-server
```

Vous pouvez réduire la taille du disque virtuel en ajoutant cette option `--hyperv-disk-size 2048` à votre ligne de commande.

```sh
docker-machine create --driver hyperv --hyperv-virtual-switch form-docker --hyperv-disk-size 2048 vm-web-server
```

### **Etape 2**: Déployer l'application `pic-viewer` sans mapping de volumes

#### Pour MacOS / Linux

```sh
eval $(docker-machine env vm-web-server)
```

![](./images/docker-machine-env.png)

Déployez l'application `pic-viewer` :

```sh
docker run -d -p 8080:5000 --restart=always ntdtfr/pic-viewer
```

#### Pour Windows

Exécutez cette commande depuis un Powershell en mode `administrateur`

```sh
docker-machine env vm-web-server | Invoke-Expression
```

![](./images/docker-machine-env-expr.png)

Déployez l'application `pic-viewer` :

```sh
docker run -d -p 8080:5000 --restart=always ntdtfr/pic-viewer
```

### Reset des variables d'environnement

```sh
eval $(docker-machine env -u)
```

## TP 7 : Swarm


docker-stack-replicas.yaml

```yaml
version: "3"
services:
  pic-viewer:
    image: ntdtfr/pic-viewer
    ports:
    - 8090:5000
    deploy:
      mode: replicated
      replicas: 2
      restart_policy:
        condition: on-failure
      placement:
        constraints: [node.role == worker]
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
docker stack deploy --compose-file docker-stack.yaml web
```

```
Creating network web_default
Creating service web_pic-viewer
```


```sh
docker service ls
```

```
ID                  NAME                MODE                REPLICAS            IMAGE                      PORTS
mfysi1tzwfgz        web_pic-viewer      replicated          2/2                 ntdtfr/pic-viewer:latest   *:8090->5000/tcp
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
