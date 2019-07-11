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
docker build --tag ntdtfr/hello-world hello-world
```

2. Exécuter le container

```sh
docker run -p 8080:80 ntdtfr/hello-world
```

3. Tester l'url : http://localhost:8080

4. Si Ok, on push l'image sur le dépôt Docker Hub

```sh
docker push ntdtfr/hello-world
```

## TP : Pinger

**Objectif** : Créer une image pour faire un « ping » d’une machine sur le réseau en se basant sur l’image officielle « alpine ».

L’adresse IP de la machine doit être passé en paramètre de la commande « docker build … », par exemple :

```sh
docker build --build-arg SERVER=192.168.xxx.xxx --tag <votre id docker hub>/pinger pinger
```

Doc : https://docs.docker.com/engine/reference/builder/


1. Construire l'image Docker

```sh
docker build --build-arg SERVER=localhost --tag ntdtfr/pinger pinger
```

2. Exécuter le container

```sh
docker run --name pinger ntdtfr/pinger
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

1. On push l'image sur le dépôt Docker Hub

```sh
docker push ntdtfr/pinger
```

Traces :

```
The push refers to repository [docker.io/ntdtfr/pinger]
256a7af3acb1: Pushed 
latest: digest: sha256:cbf0d4e021288ac28c60433a62e0ca9ee42e4a830dd4d8479afc0ad5b12b7115 size: 528
```