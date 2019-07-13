# Formation Docker

## Documentation

* Commandes CLI docker : https://docs.docker.com/engine/reference/commandline/cli/
* Commandes CLI docker-compose : https://docs.docker.com/compose/reference/overview/
* Comandes CLI docker-machine : https://docs.docker.com/machine/reference/


## TP : Hello World

**Objectif** : Créer un simple Dockerfile en se basant sur l'image tutum/hello-world.

Comment utiliser ce projet ?

1. Construire l'image Docker

```sh
docker build --rm --tag <votre id docker hub>/hello-world .
```

2. Exécuter le container

```sh
docker run --rm -p 8080:5000 <votre id docker hub>/hello-world
```

Url pour tester : http://localhost:8080

3. Si ça fonctionne, on push sur le repos Docker Hub

```sh
docker push <votre id docker hub>/hello-world
```

Exemple de traces :

```
The push refers to repository [docker.io/<votre id docker hub>/hello-world]
256a7af3acb1: Pushed 
latest: digest: sha256:cbf0d4e021288ac28c60433a62e0ca9ee42e4a830dd4d8479afc0ad5b12b7115 size: 528
```


## TP : Pinger

**Objectif** : 
* Créer une image pour faire un « ping » d’une machine sur le réseau en se basant sur l’image officielle « alpine ».
* L'adresse IP de la machine doit être passé en argument de la commande `build ...`. Par défaut, il prendra la valeur `localhost` si on ne le précise pas. Par exemple :
`
```sh
docker build --rm --build-arg SERVER=192.168.xxx.xxx --tag <votre id docker hub>/pinger .
```

Doc : https://docs.docker.com/engine/reference/builder/


1. Construire l'image Docker

```sh
docker build --rm=true --build-arg SERVER=localhost --tag <votre id docker hub>/pinger .
```

2. Exécuter le container

```sh
docker run --name pinger <votre id docker hub>/pinger
```

Traces : 

```
PING localhost (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: seq=0 ttl=64 time=0.115 ms
64 bytes from 127.0.0.1: seq=1 ttl=64 time=0.087 ms
64 bytes from 127.0.0.1: seq=2 ttl=64 time=0.072 ms
64 bytes from 127.0.0.1: seq=3 ttl=64 time=0.069 ms
64 bytes from 127.0.0.1: seq=4 ttl=64 time=0.070 ms
64 bytes from 127.0.0.1: seq=5 ttl=64 time=0.072 ms
```

3. On push l'image sur le repos Docker Hub

```sh
docker push <votre id docker hub>/pinger
```


## TP : Persistance des données




## TP : docker-compose

